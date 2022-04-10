# SpringSecurity的使用

- 依赖导入

    ```xml
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    ```

- 拦截器

    ```java
    @Configuration
    @EnableWebSecurity
    @Slf4j
    public class SecurityConfiguration extends WebSecurityConfigurerAdapter {


        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                    .csrf().disable()
                    .cors().disable()
                    .authorizeRequests()
                    .antMatchers("/login").permitAll()
                    .anyRequest().authenticated()
                    .and()
                    .formLogin()
                    .and()
                    .logout().permitAll()
                    .and().sessionManagement().maximumSessions(10).maxSessionsPreventsLogin(true).expiredUrl("/");
        }

        // spring security 必须有一个passwordEncoder
        @Bean
        public PasswordEncoder passwordEncoder() {
            return new SM3PasswordEncoder();
        }

    }

    ```

- 登录
  
    ```java
    
    @Component
    public class MyUserDetailService implements UserDetailsService {

        @Autowired
        private TUserService tUserService;

        @Override
        public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
            List<GrantedAuthority> authorities=new ArrayList<>();
            TUser tUser = tUserService.getOne(new LambdaQueryWrapper<TUser>().eq(TUser::getUserName,username));
            if (tUser==null){
                throw new UsernameNotFoundException("用户找不到");
            }
            authorities.add(new SimpleGrantedAuthority("ROLE_ADMIN"));

            User user  = new User(tUser.getUserName(),tUser.getPassword(),true,true,true,true,authorities);
            return user;
        }
    }

    ```

- 密码校验

     ```java
     
    @Slf4j
    public class SM3PasswordEncoder implements PasswordEncoder {
        @Override
        public String encode(CharSequence rawPassword) {
            return SmUtil.sm3(rawPassword.toString()).toUpperCase();
        }
        //密码校验
        @Override
        public boolean matches(CharSequence rawPassword, String encodedPassword) {
            return encodedPassword.equals(encode(rawPassword));
        }
    }
    ```
