# 建置我的個人網站之 ansible 指令稿

這個專案發展目標是打造一套可以完成我的個人網站之管理作業的 ansible playbook。

## 管理作業

這個專案裡面的 ansible playbook 會實現以下各小節提到的網站管理作業。  
請注意，開發這些 playbook 時，我假設給 ansible 用以安裝各式套件的帳號**有系統管理者權限**。  
如果給 ansible 用的帳號沒有系統權限，那許多作業將會失敗。

執行 playbook 之前有下列準備事項：

* 管理者已經在執行 playbook 的主機上安裝好 ansible。
* 管理者已經會提供欲部署 wordpress 的系統之 SSH 連線資訊給 ansible。  
  * 如果 SSH 連線使用金鑰認證使用者，那管理者會透過 ssh-agent 之類的工具幫助 ansible 連線到遠端系統。  

以上是這些 playbook 不會代為完成的事情。  

### 建置網站

在 setup-wordpress 資料內的 [setup-wordpress.yml](./setup-wordpress/setup-wordpress.yml) playbook 可以在遠端的 Centos 7 作業系統上從無到有建置 wordpress。  
setup-wordpress.yml playbook 需要依靠 configure-server 資料夾裡的 [configure-server.yml](./configure-server/configure-server.yml) playbook 設定作業系統，最後再透過 setup-wordpress-backup-scedule 資料夾裡的 [setup-wordpress-backup-scedule.yml](./setup-wordpress-backup-scedule/setup-wordpress-backup-scedule.yml) playbook 設定伺服器定期備份網站的作業。  
因此，使用者要先複製這三個資料夾──也就是 setup-wordpress、configure-server、setup-wordpress-backup-scedule──裡面以 **vars-** 開頭，**-template-.yml** 結尾的設定檔案範本為另一個去掉 -template 字串的檔案，然後填寫裡面的設定給 playbook 使用。

wordpress 的版本以及套件版本設定會記錄在 [setup-wordpress/wordpress-version.yml](setup-wordpress/wordpress-version.yml) 裡面。

### 備份網站

[backup-wordpress/backup-wordpress.yml](./backup-wordpress/backup-wordpress.yml) 可以備份遠端以單一節點運作的 wordpress 系統。  
這套 playbook 除了可用於災難後還原系統，還會用來建置一套設定、資料都和生產環境相同的開發機。  

使用前同樣要先複製資料匣裡面以 **vars-** 開頭，**-template-.yml** 結尾的設定檔案範本為另一個去掉 -template 字串的檔案，然後填寫裡面的設定給 playbook 使用。

### 還原網站

還原網站的 playbook [restore-wordpress/restore-wordpress.yml](./restore-wordpress/restore-wordpress.yml) 可以在遠端的 Centos 7 作業系統上從無到有以先前備份的紀錄還原一套 wordpress 單一節點的 wordpress。  
它的運作特性與 setup-wordpress playbook 相似，它同樣需要依靠 configure-server 資料夾裡的 [configure-server.yml](./configure-server/configure-server.yml) playbook 設定作業系統，最後再透過 setup-wordpress-backup-scedule 資料夾裡的 [setup-wordpress-backup-scedule.yml](./setup-wordpress-backup-scedule/setup-wordpress-backup-scedule.yml) playbook 設定伺服器定期備份網站的作業，因此使用者要先複製這三個資料夾──也就是 setup-wordpress、configure-server、setup-wordpress-backup-scedule──裡面以 **vars-** 開頭，**-template-.yml** 結尾的設定檔案範本為另一個去掉 -template 字串的檔案，然後填寫裡面的設定給 playbook 使用。