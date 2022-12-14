<<<<<<< Updated upstream

# PowerShell を使用してWebApp を作成する (10 分)

このチュートリアルでは、Cloud Shell を構成し、Azure PowerShell モジュールを使用してリソース グループとWebAppを作成します。

# タスク 1: Cloud Shell を設定する 

このタスクでは、Cloud Shell を構成します。 

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. Azure portal から、右上にあるアイコンをクリックして、**Azure Cloud Shell** を開きます。![wepapp01](C:\Users\CTCT\Documents\GitHub\Introduction-to-Azure-Cloud-Shell\LabManual\media\wepapp01.png)

    

3. **Bash** や **PowerShell** のどちらかを選択するためのプロンプトが表示されたら、**PowerShell** を選択します。

   > **注**: **Cloud Shell** の初回起動時に **「ストレージがマウントされていません」** というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**「ストレージの作成」** を選択します。

4. メッセージが表示されたら、**「ストレージの作成」** をクリックし、「Azure Cloud Shell」 ウィンドウが表示されるまで待ちます。

# タスク 2: リソース グループとWebAppを作成する

このタスクでは、PowerShell を使用して、リソース グループとWebAppを作成します。  

1. Cloud Shell ペインの左上のドロップダウン メニューで、**「PowerShell」** が選択されていることを確認します。

2. Powershell ウィンドウで次のコマンドを実行して、現在のリソース グループを一覧表示し確認します。

   ```PowerShell
   Get-AzResourceGroup
   ```

3. 次のコマンドをメモ帳などで編集し、ターミナル ウィンドウに貼り付けて、WebAppを作成します。 

   > **注**:「ctc**xxxx**」に入る値は**ご自身のユーザー名の番号**に変更してください。

   > **注**:「**yyyymmdd**」に入る値は**今日の日にち**に変更してください。

   ```PowerShell
   New-AzWebApp　`
   -ResourceGroupName "myRGPS" `
   -Name "ctcxxxxyyyymmdd" `
   -Location "Japan East"
   ```

4. WebApp が作成されたら、実行結果のState項目が「**Running**」になっていることを確認してください。

   ![wepapp02](C:\Users\CTCT\Documents\GitHub\Introduction-to-Azure-Cloud-Shell\LabManual\media\wepapp02.png)

   

5. PowerShell セッションの CloudShell ペインを最小化します。

6. Azure portal で「**App Service**」を検索し、作成したWebAppのWebサイトを確認してください。![wepapp03](C:\Users\CTCT\Documents\GitHub\Introduction-to-Azure-Cloud-Shell\LabManual\media\wepapp03.png)

    

# タスク 3: Cloud Shell でWebAppを操作する

このタスクでは、Cloud Shell から PowerShell コマンドを実行する練習を行います。 

1. Azure portal から、右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

2. Cloud Shell ペインの左上のドロップダウン メニューで、**「PowerShell」** が選択されていることを確認します。

3. 名前、リソース グループ、場所、状態など、WebAppに関する情報を取得します。

   ```PowerShell
   Get-AzWebApp
   ```

4. 次のコマンドを使用してWebAppを停止します。 

   > **注**:「ctc**xxxx**」に入る値は**ご自身のユーザー名の番号**に変更してください。

   > **注**:「**yyyymmdd**」に入る値は**今日の日にち**に変更してください。

   ```PowerShell
   Stop-AzWebApp -ResourceGroupName myRGPS -Name ctcxxxxyyyymmdd
   ```

5. 実行結果のState項目が「**Stopped**」に変更されたことを確認してください。

6. WebAppのWebページをもう一度確認し、停止されたことを確認してください。

   > **注**:Webページでは「Error 403 - This web app is stopped.」と表示されます。

7. 次のコマンドを実行して、作成したリソースグループとリソースを削除してください。

   > 注:自動的に削除されます。確認する場合はAzure Portal上で確認してください。

   ```PowerShell
   Get-AzResourceGroup -Name 'myRGPS' | Remove-AzResourceGroup -Force -AsJob
   ```

   

このラボでは次の内容を学習しました。

- Cloud ShellのPowerShellを使用してWebAppを作成しました。
- Cloud ShellのPowerShellを使用してWebAppを停止しました。
- Cloud ShellのPowerShellを使用してWebAppを削除しました。



```powershell
Get-AzureRmLocation | select Location
```



`otokita@gmail.com`

![flower](./media/flower.png)

# PowerShell を使用してWebApp を作成する (10 分)

このチュートリアルでは、Cloud Shell を構成し、Azure PowerShell モジュールを使用してリソース グループとWebAppを作成します。

# タスク 1: Cloud Shell を設定する 

このタスクでは、Cloud Shell を構成します。 

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. Azure portal から、右上にあるアイコンをクリックして、**Azure Cloud Shell** を開きます。![wepapp01](C:\Users\CTCT\Documents\GitHub\Introduction-to-Azure-Cloud-Shell\LabManual\media\wepapp01.png)

    

3. **Bash** や **PowerShell** のどちらかを選択するためのプロンプトが表示されたら、**PowerShell** を選択します。

   > **注**: **Cloud Shell** の初回起動時に **「ストレージがマウントされていません」** というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**「ストレージの作成」** を選択します。

4. メッセージが表示されたら、**「ストレージの作成」** をクリックし、「Azure Cloud Shell」 ウィンドウが表示されるまで待ちます。

# タスク 2: リソース グループとWebAppを作成する

このタスクでは、PowerShell を使用して、リソース グループとWebAppを作成します。  

1. Cloud Shell ペインの左上のドロップダウン メニューで、**「PowerShell」** が選択されていることを確認します。

2. Powershell ウィンドウで次のコマンドを実行して、現在のリソース グループを一覧表示し確認します。

   ```PowerShell
   Get-AzResourceGroup
   ```

3. 次のコマンドをメモ帳などで編集し、ターミナル ウィンドウに貼り付けて、WebAppを作成します。 

   > **注**:「ctc**xxxx**」に入る値は**ご自身のユーザー名の番号**に変更してください。

   > **注**:「**yyyymmdd**」に入る値は**今日の日にち**に変更してください。

   ```PowerShell
   New-AzWebApp　`
   -ResourceGroupName "myRGPS" `
   -Name "ctcxxxxyyyymmdd" `
   -Location "Japan East"
   ```

4. WebApp が作成されたら、実行結果のState項目が「**Running**」になっていることを確認してください。

   ![wepapp02](C:\Users\CTCT\Documents\GitHub\Introduction-to-Azure-Cloud-Shell\LabManual\media\wepapp02.png)

   

5. PowerShell セッションの CloudShell ペインを最小化します。

6. Azure portal で「**App Service**」を検索し、作成したWebAppのWebサイトを確認してください。![wepapp03](C:\Users\CTCT\Documents\GitHub\Introduction-to-Azure-Cloud-Shell\LabManual\media\wepapp03.png)

    

# タスク 3: Cloud Shell でWebAppを操作する

このタスクでは、Cloud Shell から PowerShell コマンドを実行する練習を行います。 

1. Azure portal から、右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

2. Cloud Shell ペインの左上のドロップダウン メニューで、**「PowerShell」** が選択されていることを確認します。

3. 名前、リソース グループ、場所、状態など、WebAppに関する情報を取得します。

   ```PowerShell
   Get-AzWebApp
   ```

4. 次のコマンドを使用してWebAppを停止します。 

   > **注**:「ctc**xxxx**」に入る値は**ご自身のユーザー名の番号**に変更してください。

   > **注**:「**yyyymmdd**」に入る値は**今日の日にち**に変更してください。

   ```PowerShell
   Stop-AzWebApp -ResourceGroupName myRGPS -Name ctcxxxxyyyymmdd
   ```

5. 実行結果のState項目が「**Stopped**」に変更されたことを確認してください。

6. WebAppのWebページをもう一度確認し、停止されたことを確認してください。

   > **注**:Webページでは「Error 403 - This web app is stopped.」と表示されます。

7. 次のコマンドを実行して、作成したリソースグループとリソースを削除してください。

   > 注:自動的に削除されます。確認する場合はAzure Portal上で確認してください。

   ```PowerShell
   Get-AzResourceGroup -Name 'myRGPS' | Remove-AzResourceGroup -Force -AsJob
   ```

   

このラボでは次の内容を学習しました。

- Cloud ShellのPowerShellを使用してWebAppを作成しました。
- Cloud ShellのPowerShellを使用してWebAppを停止しました。
- Cloud ShellのPowerShellを使用してWebAppを削除しました。











>>>>>>> Stashed changes
