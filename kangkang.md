- 代码
  - https://github.com/dingguijin/rustdesk.git
  - 改变 branch 到 kangkai
    - git checkout kangkai
- windows 上打包
  - cargo build 或者 cargo run 测试一下， 然后再执行下面的代码 build
  - python res/inline-sciter.py
  - python build.py
  - build 成功后，发布程序需要携带 sciter.dll 这个库，可以将 RustDesk.exe 和 sciter.dll 复制到一台单独的机器上测试。
- ## 测试 id code 的 fast api server

  ```
  		  import uvicorn
  		  from fastapi import FastAPI, Depends, HTTPException
  		  from fastapi.middleware.cors import CORSMiddleware
  		  from typing import Optional, List
  		  from starlette.status import HTTP_201_CREATED, HTTP_403_FORBIDDEN, HTTP_404_NOT_FOUND
  		  from pydantic import BaseModel
  		  import random
  		  import string

  		  app = FastAPI(title="FastAPI-Odoo15 App",
  		                 description="Make Odoo API",
  		                 version="0.0.1"
  		                 )

  		  class DeskCode(BaseModel):
  		       id: str
  		       code: str | None = None
  		       secret: str | None = None
  		       timestamp: str | None = None

  		  @app.post("/desk_code/", response_model=DeskCode)
  		  def desk_code(code: DeskCode):
  		      len_code = len(code.code)
  		      new_code = []
  		      for i in range(len_code):
  		          new_code.append(random.choice(string.ascii_letters))
  		      code.code = "".join(new_code)
  		      print("ID -> %s, CODE -> %s " % (code.id, code.code))
  		      return code

  		  if __name__ == '__main__':
  		       uvicorn.run(app, host="127.0.0.1", port=8000)

  ```

- 配置文件
  - Windows
    - 用户目录 AppData/Roaming/RustDesk/config/RustDesk.toml
      - kangkai_password
        - string，不用管，从服务器拿下来的密码
      - kangkai_id_server_url
        - string，id 服务 完整 url
          默认的 url http://localhost:8000/desk_code
      - kangkai_id_refresh_timeout
        - float, 多长时间去请求 id password
          默认 30 秒
-
- 在 windows 上 build RUSTDESK

  - https://rustdesk.com/docs/en/dev/build/windows/
  - 配置 rust 环境
    - https://www.rust-lang.org/tools/install
    - rustup-init.exe
    - 选择 visual studio community
    - 自动下载 几个 G 的 visual studio 的东西
  - 运行 git cmd 的终端程序 (git-bash)

    - git clone https://github.com/dingguijin/rustdesk.git
    -

    ```
    			    git clone https://github.com/microsoft/vcpkg
    			    cd vcpkg
    			    vcpkg/bootstrap-vcpkg.bat
    			    export VCPKG_ROOT=$PWD/vcpkg
    			    vcpkg/vcpkg install libvpx:x64-windows-static libyuv:x64-windows-static opus:x64-windows-static
    ```

    - 设置 vcpkg 环境变量 VCPKG_ROOT vcpkg 使用 windows 的系统设置
    - 下载 clang 并且设置环境变量，在 windows 的系统设置中
      - rust-bindgen depends on clang, download [llvm](https://github.com/llvm/llvm-project/releases) and install，add environment variable  `LIBCLANG_PATH` = `<llvm_install_dir>/bin` .
      - https://prereleases.llvm.org/win-snapshots/LLVM-12.0.0-6923b0a7-win64.exe
      - 可以直接下载安装选择设置环境变量
    - 下载 sciter.dll
      - Desktop versions use [sciter](https://sciter.com/) for GUI, please download [sciter.dll](https://raw.githubusercontent.com/c-smile/sciter-sdk/master/bin.win/x64/sciter.dll)
    -

    ```
    			  git clone https://github.com/rustdesk/rustdesk
    			  cd rustdesk
    			  mkdir -p target/debug
    			  mv sciter.dll target/debug
    			  cargo run

    ```

    -
    -

- 在 macOS 上 build RUSTDESK
  - brew install create-dmg
  - cargo install cargo-bundle
  - ./build.py
- 在 macOS 上运行，如果作为被控电脑，需要配置隐私设置，accessibility 和 screen recording
- 在 Linux 上 build RUSTDESK (Debian 11 或者找 最新的 Ubuntu，基本的过程相同)

  - 首先要配置 RUST 环境

    - https://www.rust-lang.org/tools/install
    -

    ```
    			  curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

    ```

    -
    - 安装好之后，设置环境变量，一般它是安装到你这个当前用户的目录下了。
    -

    ```
    			  source "$HOME/.cargo/env"
    ```

  - 运行 cargo，rustc 之类的命令可以看到 rust 确实安装了
  - ## 下载 RUSTDESK 代码 ，确保切换到 kangkai 的分支下面

    ```
    			  git clone git@github.com:dingguijin/rustdesk.git

    			  git checkout kangkai
    ```

  - ## 现在就编译运行，可能是过不去的，因为系统可能很多依赖的 pkg 没有安装，不过可以反复尝试，直到把所有的包都装好
    ```
    			  cargo run
    ```

- - ## 可能需要安装的包
    ```
    			  sudo apt-get install libxcb-randr0-dev libxdo-dev libgtk-3-dev clang nasm libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libxi-dev libxtst-dev libdbus-1-dev pkg-config
    ```
  - 无论 Linux 还是 macOS 都需要安装 VCPKG, 区别是 macOS 下安装 基本的软件包是 brew，而 Debian/Ubuntu 用 apt-get。但是 VCPKG 是同样的安装方法。
    - 到另外的路径，比如 $HOME 下. "FANGQIANG" FIRST (BECAUSE DOWNLOAD CODE FROM GOOGLE SITE)
    -
    ```
    			  git clone https://github.com/microsoft/vcpkg
    			  sh vcpkg/bootstrap-vcpkg.sh
    			  export VCPKG_ROOT=$HOME/vcpkg
    			  vcpkg/vcpkg install libvpx libyuv opus
    ```
    -
  - ## build rustdesk 需要编译好的 libsciter-gtk 的库，同样在 macOS 上需要下载 macOS 的库，这个是用来做前端界面的，相当于封装了一个浏览器的渲染引擎，直接可以加载 html 和 js，画出界面。
    ```
    			  cd rustdesk
    			  mkdir -p target/debug
    			  wget https://raw.githubusercontent.com/c-smile/sciter-sdk/master/bin.lnx/x64/libsciter-gtk.so
    			  mv libsciter-gtk.so target/debug
    			  VCPKG_ROOT=$HOME/vcpkg cargo run
    ```
-
-
-
- ## 条件编译
  ```
  		  #[cfg(all(unix, target_pointer_width = "32"))]
  ```
  - 条件编译类似 C 语言中的 #ifdef 之类的语句，RUST 写的更加乱糊，但是功能更强大
  -
  - 在 Linux 上没有引入 reqwest，把这个注释放开了，这样才可以在 Linux 测试，因为 HTTP 请求就是用 reqwest。
  -
  -
- ## 在函数上加 `#[inline]` 符号的作用是能够优化编译
  ```
  		  #[inline]
  		  pub fn update_temporary_password() {
  		      #[cfg(any(target_os = "android", target_os = "ios"))]
  		      password_security::update_temporary_password();
  		      #[cfg(not(any(target_os = "android", target_os = "ios")))]
  		      allow_err!(ipc::update_temporary_password());
  		  }
  ```
- tokio 异步框架

  - tokio 在系统启动的时候创建了 runtime

    - 单线程 runtime
    - 多线程 runtime
    - 一般只有明确说明才会创建单一线程的 runtime
    - > 注： `std::time` 也提供了 sleep()，但它會阻塞整個線程，而 `tokio::time` 中的 sleep()則只是讓它所在的任務放棄 CPU 並進入調度隊列等待被喚醒，它不會阻塞任何線程，它所在的線程仍然可被用來執行其它異步任務。因此，在 tokio runtime 中，應當使用 `tokio::time` 中的 sleep()。

    -

- ## reqwest 异步 http 请求

  ```
  		  let client = reqwest::Client::new();
  		              let mut map = HashMap::new();
  		              let now = SystemTime::now();
  		              let timestamp = now.duration_since(UNIX_EPOCH).unwrap().as_secs();
  		              let mut sha = Sha256::new();
  		              sha.update(timestamp.to_string());
  		              sha.update("salt");
  		              let time_hash: String = format!("{:X}", sha.finalize());
  		              log::info!("TIME HASH: {}", time_hash);

  		              map.insert("id", Config::get_id());
  		              map.insert("timestamp", timestamp.to_string());
  		              map.insert("secret", time_hash);
  		              map.insert("code", ui_interface::temporary_password());

  		              let resp = client
  		                  .post("http://localhost:8000/desk_code")
  		                  .timeout(std::time::Duration::from_millis(1500))
  		                  .json(&map)
  		                  .send()
  		                  .await;

  		              match resp {
  		                  Err(e) => {
  		                      log::error!("KANGKAI SERVER ERROR");
  		                  }
  		                  Ok(resp) => {
  		                      let r = resp.json::<HashMap<String, String>>().await?;

  		                      println!("body = {:#?}", r);
  		                      /* resp["code"] =
  		                         Config::get_auto_password(password_security::temporary_password_length());
  		                      */
  		                      Config::set_kangkai_password(r["code"].as_str());
  		                      println!("get_kangkai_password {}", Config::get_kangkai_password());
  		                  }
  		              }
  ```

  - 如果加了 ？一般是要解析 Ok 了，不加问号一般是需要 Match。
  - 如果不去 match 结果 直接使用？，那么服务器 crash 之后，这个 runtime 一直 block 或者已经死掉
  -

-
- 创建一个定时任务
  - 添加 `job_scheduler` 到你的 `Cargo.toml`
  -
  ```
  		  job_scheduler = "1.2.1"
  ```
  - 然后例子：
  -
  ```
  		  extern crate job_scheduler;
  		  use job_scheduler::{JobScheduler, Job};
  		  use std::time::Duration;
  		  fn main() {
  		    let mut sched = JobScheduler::new();
  		    sched.add(Job::new("1/10 * * * * *".parse().unwrap(), || {
  		        println!("I get executed every 10 seconds!");
  		    }));
  		    loop {
  		      sched.tick();
  		      std::thread::sleep(Duration::from_millis(500));
  		    }
  		  }
  ```
-
- ## 创建一个线程
  ```
  		  use std::thread;
  		  use std::time::Duration;
  		  - fn main() {
  		    thread::spawn(|| {
  		        for i in 1..10 {
  		            println!("hi number {} from the spawned thread!", i);
  		            thread::sleep(Duration::from_millis(1));
  		        }
  		    });
  		  - for i in 1..5 {
  		        println!("hi number {} from the main thread!", i);
  		        thread::sleep(Duration::from_millis(1));
  		    }
  		  }
  ```
  -
- 阅读源码

  - ## index.tis

    ```
    			  class PasswordEyeArea : Reactor.Component {
    			      render() {
    			          var method = handler.get_option('verification-method');
    			          var value = method != 'use-permanent-password' ? password_cache[0] : "-";
    			          return
    			              <div .eye-area style="width: *">
    			                  <input|text @{this.input} readonly value={value} />
    			                  {svg_refresh_password}
    			              </div>;
    			      }

    			      event click $(svg#refresh-password) (_, me) {
    			          handler.update_temporary_password();
    			          this.update();
    			      }
    			  }
    ```

    - 这是主界面上进行代码，更新 password 的部分，页面响应更新继续向里层传递。

  - ## 在 UI.rs 中实现了 sciter 的 update_temporary_password 的接口，真正的实现是在，ui_interface.rs 之中
    ```
    			  #[inline]
    			  pub fn update_temporary_password() {
    			      #[cfg(any(target_os = "android", target_os = "ios"))]
    			      password_security::update_temporary_password();
    			      #[cfg(not(any(target_os = "android", target_os = "ios")))]
    			      allow_err!(ipc::update_temporary_password());
    			  }
    ```
  - 这里面调用了 ipc::update_temporary_password() 这个函数是网络调用
  - 再往下 就进入 ipc 的实现 在 ipc.rs 之中
  -

  ```
  		  pub fn update_temporary_password() -> ResultType<()> {
  		      set_config("temporary-password", "".to_owned())
  		  }
  ```

  - ## 进一步就是 set_config 的实现
    ```
    			  #[tokio::main(flavor = "current_thread")]
    			  pub async fn set_config(name: &str, value: String) -> ResultType<()> {
    			      set_config_async(name, value).await
    			  }
    ```
    - 这个 flavor 是用来说明这个函数要在 main current thread 中执行吗？选择线程？
    - 有可能是连接不能跨线程引用？
  - ## 再往下是 set_config_async() 函数的实现
    ```
    			  pub async fn set_config_async(name: &str, value: String) -> ResultType<()> {
    			      let mut c = connect(1000, "").await?;
    			      c.send_config(name, value).await?;
    			      Ok(())
    			  }
    ```
  - set_config() 和 set_config_async() 都在 ipc 这个文件之中，这个文件就是进行进程间通讯的？
  - ## 往下看有个获取配置的功能 get_config()
    ```
    			  pub fn get_permanent_password() -> String {
    			      if let Ok(Some(v)) = get_config("permanent-password") {
    			          Config::set_permanent_password(&v);
    			          v
    			      } else {
    			          Config::get_permanent_password()
    			      }
    			  }
    ```
  - 不知道 permanent_password 和 temporary_password 的区别是什么？
  - ## 再往下有个 get_id() 这应该是可以用上的

    ```
    			  pub fn get_id() -> String {
    			      if let Ok(Some(v)) = get_config("id") {
    			          // update salt also, so that next time reinstallation not causing first-time auto-login failure
    			          if let Ok(Some(v2)) = get_config("salt") {
    			              Config::set_salt(&v2);
    			          }
    			          if v != Config::get_id() {
    			              Config::set_key_confirmed(false);
    			              Config::set_id(&v);
    			          }
    			          v
    			      } else {
    			          Config::get_id()
    			      }
    			  }

    ```

  - 都有谁使用了 get_id()?

- 在 CONFIG 中增加一项 KANGKAI_PASSWORD
- 在系统启动的时候，增加一个异步 RUNTIME
  - 在这个异步线程中去连接 KANGKAI 服务器
    - 通过当前时间和 salt 构造 secret
    - 带着 Config 中的 id
    - 如果在 1.5 秒之内，服务器没有返回值，就出错，睡眠 3 秒继续循环
      - 不能忽略错误，这样就没办法从错误中恢复，所以要检查 Result
      - 主要是服务器挂了可以恢复，如果一直链式调用，是可以在正常的情况下工作，但是在异常情况下就无法恢复
    - 如果有数据返回，那么就把返回的 code，更新到 kangkai password
    - password security 中获取 temporary password 的时候检查
      - Config 中的 kangkai password 是否设置
      - 如果没有设置就临时生成一个 password，如果已经设置就使用 kangkai password
- lazy_static 这个库用来将堆变量伪装成静态变量，这样就可以按照静态变量的引用方法去引用，但是可以按照堆的方式去初始化和更新数据，比如一个 HashMap，RUSTDESK 用 lazy static CLIENTS，这个 CLIENTS 管理所有连接到被控的电脑，如果想关闭所有的连接，需要遍历所有的 CLIENTS，然后挨个关掉。
  - https://cloud.tencent.com/developer/article/1744277
  - https://zhuanlan.zhihu.com/p/145893481
  -
  -
- flutter 作者可能在向 FLUTTER 转，现在的前端界面是通过浏览器访问 html，然后 js 再访问 rust 接口。如果是 flutter 是通过 flutter 的 ffi 机制访问，我们这个项目没有涉及，如果后续有变化可能会涉及，那时候 rustdesk 可能已经改成 flutter。
