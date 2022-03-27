### 修改配置文件

- vim ~/.zshrc或者vim ~/.bash_profile

  说明：`bash_profile`中修改的环境变量只对当前窗口有效，修改完`.bash_profile`之后记得在终端输入`source ~/.bash_profile`使之生效。
   `.zshrc`在电脑每次自启时都会生效（永久有效），修改完之后在终端输入`source ~/.zshrc`使之生效。

- 增加配置

  ```
  # jdk-setting
  ORACLE_JAVA_7_HOME="/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home"
  ORACLE_JAVA_8_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home"
  ORACLE_JAVA_OPOEN_8_HOME="/Users/xxxxx（替换为你的用户路径）/Library/Java/JavaVirtualMachines/corretto-1.8.0_312/Contents/Home"
  ORACLE_JAVA_OPOEN_17_HOME="/Users/xxxxx（替换为你的用户路径）/Library/Java/JavaVirtualMachines/openjdk-17.0.2/Contents/Home"
  JAVA_HOME=ORACLE_JAVA_8_HOME
  CLASS_PATH="$JAVA_HOME/lib"
  PATH=$PATH:$JAVA_HOME/bin
  alias jdk7="export JAVA_HOME=$ORACLE_JAVA_7_HOME"
  alias jdk8="export JAVA_HOME=$ORACLE_JAVA_8_HOME"
  alias openjdk8="export JAVA_HOME=$ORACLE_JAVA_OPOEN_8_HOME"
  alias openjdk17="export JAVA_HOME=$ORACLE_JAVA_OPOEN_17_HOME"
  ```

- 输入` source ~/.zshrc`让环境变量生效

- 切换jdk版本输入 jdk7或jdk8