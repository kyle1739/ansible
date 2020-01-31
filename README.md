# 使用方法

1. 安裝 Ubuntu 主機，並設定其 Internet 連線，開放 root 由 SSH 登入  
2. 編輯 hosts 檔案，設定目標的連線參數  
3. 編輯 Playbook 檔案 {playbook_name}.yml 設定目標要配置的角色  
4. 執行 ansible-playbook 配置指令：   
```$ ansible-playbook -i inventory --syntax-check --ask-vault-pass playbooks/{playbook_name}.yml```   
```$ ansible-playbook -i inventory --ask-pass --ask-vault-pass playbooks/{playbook_name}.yml```
5. 初始化部署成功以後有了 SSH Key，之後僅需執行：      
```$ ansible-playbook -i inventory -K --ask-vault-pass playbooks/{playbook_name}.yml```   

初次執行請在 hosts 設定使用 root 帳號登入，複寫 ansible_user=root 與 ansible_port=22 參數。
若有引用 Ubuntu 角色，會自動建立新的非 root sudo 帳號 (larvata)，並配置該帳號的 SSH 免密碼登入，
配置完成時會設定目標主機禁止 root 遠端 SSH 登入，下次配置則需改由 sudo 帳號 (larvata) 登入。
第二次以後執行，請先修改 hosts 檔案，將連線參數改回預設的 (帳號 larvata 與 8022 port)。

## ansible-playbook 選項

* -v 顯示詳細的執行過程  
* -K 授予 sudo 權限，提示輸入 sudo 密碼  
* --ask-pass 使用 SSH 密碼登入目標主機，而不是使用 SSH Key 登入  
* --ask-vault-pass 儲存帳號預設密碼的 vars/main.yml 使用了 ansible-vault 加密，因此需輸入解密的密碼  
* --private-key=~/.ssh/id_rsa 使用指定的 SSH Private Key 作為登入手段   
* --check 可以测试脚本执行情况，但实际并未在远程机器执行   
* --tags 提示 ansible-play-book 调用哪些 tags 命令   

## 其他指令

* ansible all -m setup   
讓 Ansible 顯示所有主機的可用參數

* ansible-vault create vars/main.yml   
建立一個加密的 vars 變數檔案，通常用來儲存預設密碼，   
但是預設密碼的值需要先由 mkpasswd --method=SHA-512 指令產出。

* ansible-vault edit vars/main.yml   
編輯加密過的 vars 變數檔案，會需要輸入當初加密的密碼

* ansible-vault rekey vars/main.yml   
修改 vars 變數檔案的加密密碼

# 目錄結構

```
├── Makefile // 定義一些常用任務，例如 make clean  
├── README.md   
├── ansible.cfg // Ansible 運行的設定檔   
├── hosts // 定義目標主機群，以及設定預設的登入配置   
├── playbooks   
│   └── {playbook_name}.yml // Ansible 執行目標與角色設定   
└── roles   
    └── {role_name} // Ansible 角色目錄   
        ├── files   
        │   └── {file_name} // 需要被傳送到遠端主機的各種文件   
        ├── templates   
        │   └── {template_name} // 各種內容樣板   
        ├── meta   
        │   └── main.yml // 角色相依性設定   
        ├── tasks   
        │   └── main.yml // 角色主要運行邏輯，ansible-playbook 執行的進入點
        ├── handlers   
        │   └── main.yml // 定義通用的操作任務，例如 service httpd restart     
        └── vars   
            └── main.yml // 通用的配置變數  
``` 

# 參考文獻

* https://serversforhackers.com/series/ansible  
* https://github.com/zheng-ji/ToyCollection/tree/master/my-ansible  
* https://gist.github.com/tristanfisher/e5a306144a637dc739e7  
* http://zheng-ji.info/blog/2015/09/05/ansiblede-shi-yong-jing-yan/  
* https://www.ptt.cc/bbs/Soft_Job/M.1459614489.A.583.html   
* https://ingramchen.io/blog/2014/06/vagrant-up.html   
* http://martinfowler.com/bliki/ImmutableServer.html   
* http://techblog.netflix.com/2016/03/how-we-build-code-at-netflix.html  
* http://www.codelitt.com/blog/my-first-10-minutes-on-a-server-primer-for-securing-ubuntu/  
