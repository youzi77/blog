# 功能介绍
该功能主要是在项目中集成quartz定时任务，以及定时任务的持久化配置。资料的大多数都是直接引入quartz的11个表然后设置quartz的连接数据来实现，我觉的相对于quartz的直连数据库这种方式相对来说简单一些
# 代码
1. pom文件配置quartz
    ```xml
     <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-quartz</artifactId>
    </dependency>
    ```
2. 定时任务配置文件
    **任务调度配置**
    ```java
    /**
    * 任务调度配置
    *
    * @author youzi
    * @date 2019/1/18 17:59
    */
    @Configuration
    public class QuartzConfig {
    
        @Bean
        public SchedulerFactoryBean schedulerFactoryBean(){
            SchedulerFactoryBean schedulerFactoryBean = new SchedulerFactoryBean();
            schedulerFactoryBean.setOverwriteExistingJobs(true);//覆盖已存在任务
            return schedulerFactoryBean;
        }
    
        // 创建schedule
        @Bean(name = "scheduler")
        public Scheduler scheduler() {
            return schedulerFactoryBean().getScheduler();
        }
    }
    ```
    **定时任务工厂配置**
    
    ```java
    public class QuartzFactory implements Job {
    
        @Override
        public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
    
            //获取调度数据
            ScheduleJob scheduleJob = (ScheduleJob) jobExecutionContext.getMergedJobDataMap().get("scheduleJob");
    
            //获取对应的Bean
            Object object = SpringUtil.getBean(scheduleJob.getBeanName());
            try {
                //利用反射执行对应方法
                Method method = object.getClass().getMethod(scheduleJob.getMethodName());
                method.invoke(object);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```
3. 定时任务配置实体类
    ```java
    
    @Data
    @TableName(value = "SCHEDULE_JOB")
    public class ScheduleJob {
        /**
        * 任务id
        */
        @TableId(value = "ID", type = IdType.ASSIGN_ID)
        private Long id;
    
        /**
        * 任务名称
        */
        @TableField(value = "JOB_NAME")
        private String jobName;
    
        /**
        * cron表达式
        */
        @TableField(value = "CRON_EXPRESSION")
        private String cronExpression;
    
        /**
        * 服务名称
        */
        @TableField(value = "BEAN_NAME")
        private String beanName;
    
        /**
        * 方法名称
        */
        @TableField(value = "METHOD_NAME")
        private String methodName;
    
        /**
        * 状态 1.启动 2.暂停
        */
        @TableField(value = "STATUS")
        private Integer status;
    
        /**
        * 是否删除 0.否 1.是
        */
        @TableField(value = "DELETE_FLAG")
        private Integer deleteFlag;
    
        /**
        * 创建人id
        */
        @TableField(value = "CREATE_USER_ID")
        private Long createUserId;
    
        /**
        * 创建人名称
        */
        @TableField(value = "CREATE_USER_NAME")
        private String createUserName;
    
        /**
        * 更新时间
        */
        @TableField(value = "UPDATE_TIME")
        private Date updateTime;
    
        /**
        * 创建时间
        */
        @TableField(value = "CREATE_TIME")
        private Date createTime;
    }    
    ```
4. 定时任务service层
   
    service层有两个文件其中一个是与数据库交互的service，另一个是quartz调用定时开始暂停以及删除所用

    **数据库操作service**
    ```java
    @Service
    public class ScheduleJobServiceImpl extends ServiceImpl<ScheduleJobMapper, ScheduleJob> implements ScheduleJobService{
    
        @Autowired
        private QuartzService quartzService;
    
        @Override
        public PageBean<ScheduleJob> selectPageList(Map<String, Object> params) {
            return new PageBean(this.baseMapper.selectPage(new PageQuery<ScheduleJob>(params).getPage(),new QueryWrapper<>()));
        }
    
        @Override
        public void add(ScheduleJob job) {
            
            this.save(job);
            try {
                if (job.getStatus().equals(JobOperateEnum.START.getValue())){
                    quartzService.addJob(job);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    
        @Override
        public void updateJob(ScheduleJob job) {
            this.updateById(job);
            if (job.getDeleteFlag()!=1&& job.getStatus().equals(JobOperateEnum.START.getValue())){
                try {
                    quartzService.operateJob(JobOperateEnum.PAUSE,job);
                    quartzService.operateJob(JobOperateEnum.START,job);
                } catch (SchedulerException e) {
                    e.printStackTrace();
                }
            }
        }
    
        @Override
        public void start(Long id) {
            
            ScheduleJob job = this.getById(id);
            job.setStatus(JobOperateEnum.START.getValue());
            job.setDeleteFlag(0);
            this.updateById(job);
    
            
            try {
                quartzService.operateJob(JobOperateEnum.START, job);
            } catch (SchedulerException e) {
                e.printStackTrace();
            }
        }
    
        @Override
        public void pause(Long id) {
            
            ScheduleJob job = this.getById(id);
            job.setStatus(JobOperateEnum.PAUSE.getValue());
            this.updateById(job);
    
            
            try {
                quartzService.operateJob(JobOperateEnum.PAUSE, job);
            } catch (SchedulerException e) {
                e.printStackTrace();
            }
        }
    
        @Override
        public void delete(Long id) {
            
            ScheduleJob job = this.getById(id);
            job.setDeleteFlag(1);
            job.setStatus(JobOperateEnum.DELETE.getValue());
            this.updateById(job);
            try {
                quartzService.operateJob(JobOperateEnum.DELETE, job);
            } catch (SchedulerException e) {
                e.printStackTrace();
            }
        }
    
        @Override
        public void startAllJob() {
            
            ScheduleJob job = new ScheduleJob();
            job.setStatus(1);
            this.update(job, new QueryWrapper<>());
    
            
            try {
                quartzService.startAllJob();
            } catch (SchedulerException e) {
                e.printStackTrace();
            }
        }
    
        @Override
        public void pauseAllJob() {
            
            ScheduleJob job = new ScheduleJob();
            job.setStatus(2);
            this.update(job, new QueryWrapper<>());
    
            
            try {
                quartzService.pauseAllJob();
            } catch (SchedulerException e) {
                e.printStackTrace();
            }
        }
    }
    ```
    **定时任务的service**
    ```java
    
    @Service
    @Transactional
    @Slf4j
    public class QuartzServiceImpl implements QuartzService {
    
        /**
        * 调度器
        */
        @Autowired
        private Scheduler scheduler;
    
        @Autowired
        private ScheduleJobService jobService;
    
        @Override
        public void timingTask() {
    
            //查询数据库是否存在需要定时的任务
            List<ScheduleJob> scheduleJobs = jobService.list(new QueryWrapper<ScheduleJob>().eq("DELETE_FLAG",0).eq("STATUS",1));
            if (scheduleJobs != null) {
                scheduleJobs.forEach(this::addJob);
            }
        }
    
        @Override
        public void addJob(ScheduleJob job) {
            try {
                //创建触发器
                Trigger trigger = TriggerBuilder.newTrigger().withIdentity(job.getJobName())
                        .withSchedule(CronScheduleBuilder.cronSchedule(job.getCronExpression()))
                        .startNow()
                        .build();
    
                //创建任务
                JobDetail jobDetail = JobBuilder.newJob(QuartzFactory.class)
                        .withIdentity(job.getJobName())
                        .build();
    
                //传入调度的数据，在QuartzFactory中需要使用
                jobDetail.getJobDataMap().put("scheduleJob", job);
    
                //调度作业
                scheduler.scheduleJob(jobDetail, trigger);
    
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    
        @Override
        public void operateJob(JobOperateEnum jobOperateEnum, ScheduleJob job) throws SchedulerException {
            JobKey jobKey = new JobKey(job.getJobName());
            JobDetail jobDetail = scheduler.getJobDetail(jobKey);
            if (jobDetail == null) {
                log.info("job调度器为空重新创建");
                this.addJob(job);
                //抛异常
            }
            switch (jobOperateEnum) {
                case START:
                    scheduler.resumeJob(jobKey);
                    break;
                case PAUSE:
                    scheduler.pauseJob(jobKey);
                    break;
                case DELETE:
                    scheduler.deleteJob(jobKey);
                    break;
            }
        }
    
        @Override
        public void startAllJob() throws SchedulerException {
            scheduler.start();
        }
    
        @Override
        public void pauseAllJob() throws SchedulerException {
            scheduler.standby();
        }
    }
    ```
    **最后两个service的接口层**
    ```java
    public interface ScheduleJobService extends IService<ScheduleJob>{
    /**
     * 功能描述:  查询所有定时任务
     *
     * @param params limit 数量 page 第几页
     * @return: com.weichai.timing.util.PageBean<com.weichai.timing.module.entity.ScheduleJob>
     * @author: youzi
     * @date: 2020/3/9 9:47 下午
     */
    PageBean<ScheduleJob> selectPageList(Map<String,Object> params);
    /**
     * 新增定时任务
     *
     * @author youzi
     * @date 2019/1/28 15:37
     * @param job 任务
     */
    void add(ScheduleJob job);
    /**
     * 功能描述: 更新定时任务
     *
     * @param job 1
     * @return: void
     * @author: youzi
     * @email: 704273004@qq.com
     * @date: 2020/3/11 12:22 下午
     */
    void updateJob(ScheduleJob job);
    
    /**
     * 启动定时任务
     *
     * @author youzi
     * @date 2019/1/28 16:49
     * @param id 任务id
     */
    void start(Long id);
    
    /**
     * 暂停定时任务
     *
     * @author youzi
     * @date 2019/1/28 16:49
     * @param id 任务id
     */
    void pause(Long id);
    
    /**
     * 删除定时任务
     *
     * @author youzi
     * @date 2019/1/28 16:49
     * @param id 任务id
     */
    void delete(Long id);
    
    /**
     * 启动所有定时任务
     *
     * @author youzi
     * @date 2019/1/28 16:49
     */
    void startAllJob();
    
    /**
     * 暂停所有定时任务
     *
     * @author youzi
     * @date 2019/1/28 16:49
     */
    void pauseAllJob();
    
    }
    
    //QuartzService接口
    
    public interface QuartzService {
    
        /**
        * 服务器启动执行定时任务
        *
        * @author youzi
        * @date 2019/1/28 15:38
        */
        void timingTask();
    
        /**
        * 新增定时任务
        *
        * @author youzi
        * @date 2019/1/28 15:44
        * @param job 任务
        */
        void addJob(ScheduleJob job);
    
        /**
        * 操作定时任务
        *
        * @author youzi
        * @date 2019/1/28 16:56
        * @param jobOperateEnum 操作枚举
        * @param job 任务
        */
        void operateJob(JobOperateEnum jobOperateEnum, ScheduleJob job) throws SchedulerException;
    
        /**
        * 启动所有任务
        *
        * @author youzi
        * @date 2019/1/28 16:58
        */
        void startAllJob() throws SchedulerException;
    
        /**
        * 暂停所有任务
        *
        * @author youzi
        * @date 2019/1/28 16:58
        */
        void pauseAllJob() throws SchedulerException;
    }
    ```
5. 定时任务控制器
    ```java
    
    @RestController
    @RequestMapping("/job")
    public class JobController {
    
        @Autowired
        private ScheduleJobService jobService;
            @GetMapping("/page")
        public Object pageList(Map<String,Object> params){
            return jobService.selectPageList(params);
        }
        
        @GetMapping("/page")
        public Object pageList(Map<String,Object> params){
            return jobService.selectPageList(params);
        }
    @RequestMapping("/add")
        public String add(
                @RequestParam String jobName,
                @RequestParam String cronExpression,
                @RequestParam String beanName,
                @RequestParam String methodName
        ) {
            ScheduleJob job = new ScheduleJob();
            job.setJobName(jobName);
            job.setCronExpression(cronExpression);
            job.setBeanName(beanName);
            job.setMethodName(methodName);
            job.setDeleteFlag(0);
            job.setStatus(JobOperateEnum.PAUSE.getValue());
            jobService.add(job);
            return "新增定时任务成功";
        }
    
        @RequestMapping("/updateJob")
        public String updateJob(
                @RequestParam Long id,
                @RequestParam String jobName,
                @RequestParam String cronExpression,
                @RequestParam String beanName,
                @RequestParam String methodName
        ) {
            ScheduleJob job = jobService.getById(id);
            job.setJobName(jobName);
            job.setCronExpression(cronExpression);
            job.setBeanName(beanName);
            job.setMethodName(methodName);
    //        job.setDeleteFlag(0);
    //        job.setStatus(JobOperateEnum.PAUSE.getValue());
            jobService.updateJob(job);
            return "修改定时任务成功";
        }
        @GetMapping("/start")
        public String start(@RequestParam("id") Long id) {
            jobService.start(id);
            return "启动定时任务成功";
        }
    
        @GetMapping("/pause")
        public String pause(@RequestParam("id") Long id) {
            jobService.pause(id);
            return "暂停定时任务成功";
        }
    
        @GetMapping("/delete")
        public String delete(@RequestParam("id") Long id) {
            jobService.delete(id);
            return "删除定时任务成功";
        }
    
        @GetMapping("/startAllJob")
        public String startAllJob() {
            jobService.startAllJob();
            return "启动所有定时任务成功";
        }
    
        @GetMapping("/pauseAllJob")
        public String pauseAllJob() {
            jobService.pauseAllJob();
            return "暂停所有定时任务成功";
        }
    }

6. 定时任务项目运行就启动（可选）
   ```java
    @Component
    @Slf4j
    public class JobSchedule implements CommandLineRunner {
   
        @Autowired
        private QuartzService taskService;
   
        /**
        * 任务调度开始
        * @param strings
        * @throws Exception
        */
        @Override
        public void run(String... strings) throws Exception {
            log.info("任务调度开始==============任务调度开始");
            taskService.timingTask();
            log.info("任务调度结束==============任务调度结束");
        }
    }   
   ```
7. 定时任务job示例
    ```java
    @Component("testJob01")
    @Slf4j
    public class TestJob01 {
    
        public void execute() {
            log.info("-------------------TestJob01任务执行开始-------------------");
            log.info(new Date()+"");
            log.info("-------------------TestJob01任务执行结束-------------------");
        }
    }
8. 前端代码，前端简单的用了一下vue和element添加数据代码如下
   ```html
   <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>定时任务页面</title>
        <link rel="stylesheet" href="https://unpkg.com/element-ui@2.0.5/lib/theme-chalk/index.css">
        <script src="https://unpkg.com/vue/dist/vue.js"></script>
        <script src="http://cdn.bootcss.com/vue-resource/1.3.4/vue-resource.js"></script>
        <script src="https://unpkg.com/element-ui@2.0.5/lib/index.js"></script>
   
        <style>
            #top {
                background: #20A0FF;
                padding: 5px;
                overflow: hidden
            }
        </style>
   
    </head>
    <body>
    <div id="test">
   
        <div id="top">
            <el-button  @click="search" type="success" round style="color:white">查询</el-button>
            <el-button  @click="handleadd" type="success" round style="color:white">添加</el-button>
   
            <!-- <el-button href="/log" target="_blank"><el-button  type="warning" round style="color:darkred">实时log日志</el-button></a> -->
            <!-- <el-button href="/fileHistory" target="_blank"><el-button type="warning" round  style="color:darkred">历史log日志</el-button></a> -->
            </span>
        </div>
   
        <br/>
   
        <div style="margin-top:15px">
   
            <el-table
                    ref="testTable"
                    :data="tableData"
                    style="width:100%"
                    border
            >
                <el-table-column
                        prop="id"
                        label="任务id"
                        sortable
                        show-overflow-tooltip>
                </el-table-column>
                <el-table-column
                        prop="jobName"
                        label="任务名称"
                        sortable
                        show-overflow-tooltip>
                </el-table-column>
   
                <el-table-column
                        prop="cronExpression"
                        label="cron表达式"
                        sortable>
                </el-table-column>
   
                <el-table-column
                        prop="beanName"
                        label="服务名"
                        sortable>
                </el-table-column>
   
                <el-table-column
                        prop="methodName"
                        label="方法名"
                        sortable>
                </el-table-column>
   
                <el-table-column
                        prop="status"
                        label="定时任务状态"
                        :formatter="formatterStatus"
                        sortable>
                </el-table-column>
   
                <el-table-column
                        prop="deleteFlag"
                        label="删除状态"
                        :formatter="formatterDel"
                        sortable>
                </el-table-column>
                    <el-table-column label="操作" width="300">
                   <template scope="scope">
                       <el-button
                               size="small"
                               type="warning"
                               @click="handlePause(scope.$index, scope.row)">暂停
                       </el-button>
   
                       <el-button
                               size="small"
                               type="info"
                               @click="handleResume(scope.$index, scope.row)">恢复
                       </el-button>
   
                       <el-button
                               size="small"
                               type="danger"
                               @click="handleDelete(scope.$index, scope.row)">删除
                       </el-button>
   
                       <el-button
                               size="small"
                               type="success"
                               @click="handleUpdate(scope.$index, scope.row)">修改
                       </el-button>
                   </template>
               </el-table-column>
           </el-table>
   
           <div align="center">
               <el-pagination
                       @size-change="handleSizeChange"
                       @current-change="handleCurrentChange"
                       :current-page="currentPage"
                       :page-sizes="[10, 20, 30, 40]"
                       :page-size="pagesize"
                       layout="total, sizes, prev, pager, next, jumper"
                       :total="totalCount">
               </el-pagination>
           </div>
       </div>
   
       <el-dialog title="添加任务" :visible.syn="dialogFormVisible">
           <el-form :model="form">
               <el-form-item label="任务名称" label-width="120px" style="width:50%">
                   <el-input v-model="form.jobName" auto-complete="off"></el-input>
               </el-form-item>
               <el-form-item label="cron表达式" label-width="120px" style="width:50%">
                   <el-input v-model="form.cronExpression" auto-complete="off"></el-input>
               </el-form-item>
               <el-form-item label="服务名" label-width="120px" style="width:50%">
                   <el-input v-model="form.beanName" auto-complete="off"></el-input>
               </el-form-item>
               <el-form-item label="方法名" label-width="120px" style="width:50%">
                   <el-input v-model="form.methodName" auto-complete="off"></el-input>
               </el-form-item>
           </el-form>
           <div slot="footer" class="dialog-footer">
               <el-button @click="dialogFormVisible = false">取 消</el-button>
               <el-button type="primary" @click="add">确 定</el-button>
           </div>
       </el-dialog>
   
       <el-dialog title="修改任务" :visible.syn="updateFormVisible">
           <el-form :model="updateform">
               <el-form-item label="任务名称" label-width="120px" style="width:50%">
                   <el-input v-model="updateform.jobName" auto-complete="off"></el-input>
               </el-form-item>
               <el-form-item label="cron表达式" label-width="120px" style="width:50%">
                   <el-input v-model="updateform.cronExpression" auto-complete="off"></el-input>
               </el-form-item>
               <el-form-item label="服务名" label-width="120px" style="width:50%">
                   <el-input v-model="updateform.beanName" auto-complete="off"></el-input>
               </el-form-item>
               <el-form-item label="方法名" label-width="120px" style="width:50%">
                   <el-input v-model="updateform.methodName" auto-complete="off"></el-input>
               </el-form-item>
           </el-form>
           <div slot="footer" class="dialog-footer">
               <el-button @click="updateFormVisible = false">取 消</el-button>
               <el-button type="primary" @click="update">确 定</el-button>
           </div>
       </el-dialog>
   
   </div>
   
   <footer align="center">
       <p>&copy; 微服务 任务管理</p>
   </footer>
   
   <script>
       var vue = new Vue({
           el: "#test",
           data: {
               //表格当前页数据
               tableData: [],
   
               //请求的URL
               url: 'job/page',
   
               //默认每页数据量
               pagesize: 10,
   
               //当前页码
               currentPage: 1,
   
               //查询的页码
               start: 1,
   
               //默认数据总数
               totalCount: 1000,
   
               //添加对话框默认可见性
               dialogFormVisible: false,
   
               //修改对话框默认可见性
               updateFormVisible: false,
   
               //提交的表单
               form: {
                   jobName: '',
                   jobGroup: '',
                   cronExpression: '',
               },
   
               updateform: {
                   jobName: '',
                   jobGroup: '',
                   cronExpression: '',
               },
           },
   
           methods: {
   
               //从服务器读取数据
               loadData: function (pageNum, pageSize) {
                   this.$http.get('job/page?' + 'page=' + pageNum + '&limit=' + pageSize).then(function (res) {
                       console.log(res)
                       this.tableData = res.body.list;
                       this.totalCount = res.body.totalCount;
                   }, function () {
                       console.log('failed');
                   });
               },
   
               //单行删除
               handleDelete: function (index, row) {
                   this.$http.get('job/delete?'+"id="+row.id).then(function (res) {
                       this.loadData(this.currentPage, this.pagesize);
                   }, function () {
                       console.log('failed');
                   });
               },
   
               //暂停任务
               handlePause: function (index, row) {
                   this.$http.get('job/pause?'+"id="+row.id ).then(function (res) {
                       this.loadData(this.currentPage, this.pagesize);
                   }, function () {
                       console.log('failed');
                   });
               },
   
               //恢复任务
               handleResume: function (index, row) {
                   this.$http.get('job/start?'+"id="+row.id).then(function (res) {
                       this.loadData(this.currentPage, this.pagesize);
                   }, function () {
                       console.log('failed');
                   });
               },
   
               //搜索
               search: function () {
                   this.loadData(this.currentPage, this.pagesize);
               },
   
               //弹出对话框
               handleadd: function () {
                   this.dialogFormVisible = true;
               },
   
               //添加
               add: function () {
                   this.$http.post('job/add',
                       {
                           "jobName": this.form.jobName,
                           "cronExpression": this.form.cronExpression,
                           "beanName": this.form.beanName,
                           "methodName": this.form.methodName
                       }, {emulateJSON: true}).then(function (res) {
                       this.loadData(this.currentPage, this.pagesize);
                       this.dialogFormVisible = false;
                   }, function () {
                       console.log('failed');
                   });
               },
   
               //更新
               handleUpdate: function (index, row) {
                   console.log(row)
                   this.updateFormVisible = true;
                   this.updateform.id = row.id;
                   this.updateform.jobName = row.jobName;
                   this.updateform.cronExpression = row.cronExpression;
                   this.updateform.beanName = row.beanName;
                   this.updateform.methodName = row.methodName;
               },
   
               //更新任务
               update: function () {
                   this.$http.post('job/updateJob',
                       {
                           "id": this.updateform.id,
                           "jobName": this.updateform.jobName,
                           "cronExpression": this.updateform.cronExpression,
                           "beanName": this.updateform.beanName,
                           "methodName": this.updateform.methodName
                       }, {emulateJSON: true}
                   ).then(function (res) {
                       this.loadData(this.currentPage, this.pagesize);
                       this.updateFormVisible = false;
                   }, function () {
                       console.log('failed');
                   });
   
               },
   
               //每页显示数据量变更
               handleSizeChange: function (val) {
                   this.pagesize = val;
                   this.loadData(this.currentPage, this.pagesize);
               },
   
               //页码变更
               handleCurrentChange: function (val) {
                   this.currentPage = val;
                   this.loadData(this.currentPage, this.pagesize);
               },
               formatterStatus: function (value) {
                   if (value.status == 1) {
                       return "运行"
                   } else if (value.status == 2) {
                       return "暂停"
                   } else {
                       return "删除"
                   }
               },
               formatterDel: function (value) {
                   if (value.deleteFlag == 0) {
                       return "正常"
                   } else {
                       return "删除"
                   }
               }
           },
               });
   
       //载入数据
       vue.loadData(vue.currentPage, vue.pagesize);
   </script>
   
   </body>
   </html>





9. 使用方法
    代码集成完毕之后直接添加就可以了，开箱即用
# 说明
上面的代码集成里面并没有，Mapper的实现，与数据库操作的service需要mapper，我使用了mybatisplus使用这个比较方便比较快。