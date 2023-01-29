# <a name="lab-answer-key-implementing-identity-services-and-group-policy"></a>Lab01: ID サービスおよびグループ ポリシーの実装

### **<a name="scenario"></a>シナリオ**

あなたは Contoso Ltd. に管理者として勤務しています。この会社は、いくつかの新しい場所でビジネスを拡大しています。 現在、Active Directory Domain Services (AD DS) 管理チームは、非対話型のリモート ドメイン コントローラーの展開のために Windows Server で使用できる方法を評価しています。 また、チームは特定の AD DS 管理タスクを自動化する方法を探しています。 さらに、チームは、グループ ポリシー オブジェクト (GPO) に基づいて構成管理を確立したいと考えています。

## <a name="objectives"></a>目標

このラボを完了すると、次のことができるようになります。

- Server Core に新しいドメイン コントローラーを展開する。
- グループ ポリシーを構成する。

## <a name="estimated-time-45-minutes"></a>予想所要時間: 45 分



## <a name="exercise-1-deploying-a-new-domain-controller-on-server-core"></a>演習 1: Server Core への新しいドメイン コントローラーの展開

### <a name="task-1-deploy-ad-ds-on-a-new-windows-server-core-server"></a>タスク 1: Server Core に AD DS を展開する

1. **SEA-ADM1** に以下の資格情報でサインインします。

   | 資格情報       |                            |
   | -------------- | -------------------------- |
   | **ユーザー名** | **Contoso\\Administrator** |
   | **パスワード** | **Pa55w.rd**               |

1. **SEA-ADM1** で **[スタート]** メニューから、**[Windows PowerShell (管理者)]** をクリックします。

1. 以下のWindows PowerShellコマンドレットを実行し、**SEA-SVR1**にAD DS (Active Directory ドメインサービス)の役割をインストールします。

   ```powershell
   Install-WindowsFeature –Name AD-Domain-Services –ComputerName SEA-SVR1
   ```

   **※インストールが完了するまでに数分時間を要します。**

   **※実行結果の[Success]が[True]とかえってくれば、インストール完了です。**

   ![InstallADDS](./media/InstallADDS.png)

1. **SEA-SVR1** に AD DS 役割がインストールされていることを確認するには、**SEA-ADM1**で以下のWindows PowerShell コマンドレットを実行します。

   ```powershell
   Get-WindowsFeature –ComputerName SEA-SVR1
   ```

1. 4のコマンドレット実行結果で、**Active Directory Domain Services** を探し、**[X]** が表示されていれば、**SEA-SVR1**にインストール済みです。

   ![AZ-800_Lab1_02](./media/AZ-800_Lab1_02.png)

   > **注: インストール プロセスが完了してから、AD DS の役割がインストールされていることが確認できるようになるまで、時間を要する場合があります。 Get-WindowsFeature コマンドレットから結果が得られない場合は、時間をおいて再度実行してみてください。**

### <a name="task-2-prepare-the-ad-ds-installation-and-promote-a-remote-server"></a>タスク 2: AD DS インストールの準備をして、リモート サーバーを昇格させる

1. **SEA-ADM1** の **[スタート]** メニューから[Server Manager] を選択して起動させます。起動後、左ペインから **[All Servers(すべてのサーバー)]** を選択します。

   <img src="./media/AZ-800_Lab1_03.png" alt="AZ-800_Lab1_03" style="zoom:80%;" />

2. **[Manage(管理)]** メニューで、**[Add Servers(サーバーの追加)]** を選択します。

   ![AZ-800_Lab1_04](./media/AZ-800_Lab1_04.png)
   
3. **[Add Servers(サーバーの追加)]** ダイアログ ボックスで、 **[Find Now(今すぐ検索)]** をクリックします。

![AZ-800_Lab1_05](./media/AZ-800_Lab1_05.png)

   4.サーバーの **[Active Directory]** リストで **[SEA-SVR1]** を選び、矢印を選択して **[選択済み]** リストに追加してから、**[OK]** を選択します。

<img src="./media/AZ-800_Lab1_06.png" alt="AZ-800_Lab1_06" style="zoom:80%;" />



   5.**SEA-ADM1** の **[Server Manager]** の左ペインから **[AD DS]** を選択し、**SEA-SRV1** への AD DS 役割のインストールが完了していることを確認します。

![AZ-800_Lab1_07](./media/AZ-800_Lab1_07.png)



   6.**[Server Manager]**の右上にある**[通知]** フラグ をクリックします。**SEA-SVR1** の展開後の構成に注目します。**[ Promote this server to a domain controller (このサーバーをドメイン コントローラーに昇格する)]** のリンクをクリックします。

![AZ-800_Lab1_08](./media/AZ-800_Lab1_08.png)

> **注**: **SEA-SVR1**にAD DSの役割をインストールしただけではドメインコントローラーにならないため、昇格させる必要があります。

**※通知フラグに[Refresh failed]エラーが表示される場合がありますが、ラボと直接関係のないエラーのため無視して構いません。**

   7.**[Active Directory Domain Services Configuration ウィザード]** の **[Deployment Configuration(展開構成)]** ページの **[Select the deployment operation (展開操作の選択)]**  の下で、**[Add a domain controller to an existing domain(既存のドメインにドメイン コントローラーを追加する)]** が選択されていることを確認します。

![AZ-800_Lab1_09](./media/AZ-800_Lab1_09.png)

   8.`Contoso.com` ドメインが指定されていることを確かめてから、**[Supply the credentials to perform this operation (この操作を実行する資格情報を指定する)]** セクションで **[Change (変更)]** を選択します。

 

![AZ-800_Lab1_10](./media/AZ-800_Lab1_10.png)

   9.**[資格情報]** ダイアログ ボックスに以下の資格情報を入力し、**[OK]** をクリックします。

| 資格情報       |                            |
| -------------- | -------------------------- |
| **ユーザー名** | **Contoso\\Administrator** |
| **パスワード** | **Pa55w.rd**               |

10. 資格情報が変更されたことを確認し、**[Next (次へ)]** をクリックします。

11. **[Domain Controller Options]** ページで、**[Domain Name System (DNS) server]** と **[Global Catalog (GC)]** チェックボックスがオンになっていることを確認します。 **[読み取り専用ドメイン コントローラー (RODC)]** チェック ボックスがオフで構いません。

12. **[Type the Directory Services Restore Mode (DSRM) password (ディレクトリ サービス復元モード (DSRM) パスワードの入力)]** セクションで、以下のパスワードを入力し、**[次へ]** をクリックします。

| パスワード   |
| ------------ |
| **Pa55w.rd** |

![AZ-800_Lab1_11](./media/AZ-800_Lab1_11.png)



13. **[DNS Options]** ページでは規定値のまま、**[Next (次へ)]** をクリックします。

※ページ内に、DNSゾーンの委任に関する警告メッセージが表示されますが、このラボではDNSゾーンの委任は行わないため無視して構いません。

14. **[Additional Options (追加オプション)]** ページで、規定値のまま **[Next (次へ)]**  をクリックします。

※IFMオプションはチェックを外したままで構いません。

15. **[Paths (パス)]** ページでは、既定のパスのまま、**[Next (次へ)]** をクリックします。

16. **[Review Options (オプションの確認)]**ページでは、**[Next (次へ)]** をクリックします。

17.  **[Prerequisites Check (前提条件のチェック)]** ページで、緑のチェックが表示されたことを確認したら **[Install (インストール)]** をクリックします。

 **[This server was successfully configured as a domain contoroller]** のメッセージが表示されたら、 **[Close (閉じる)]** をクリックします。

18. **SEA-ADM1** で **[Server Manager]** に切り替え、右上の更新ボタンをクリックします。その後、通知メッセージを確認し **[ Promote this server to a domain controller (このサーバーをドメイン コントローラーに昇格する)]**  が表示されなくなっていることを確認します。

> **注**:SEA-SVR1の再起動が完了するまでに何度か **[更新]** を繰り返す必要がある場合があります。

![AZ-800_Lab1_12](./media/AZ-800_Lab1_12.png)



### <a name="task-3-manage-objects-in-ad-ds"></a>タスク 3: AD DS でオブジェクトを管理する

1. **SEA-ADM1** で **Windows PowerShell (管理者)** に切り替えます。

1. 以下のWindows PowerShellコマンドレットを実行し、**SEA-DC1** のドメインコントローラーに **Seattle** という組織単位 (OU) を作成します。

   ```powershell
   New-ADOrganizationalUnit -Name "Seattle" -Path "DC=contoso,DC=com" -ProtectedFromAccidentalDeletion $true -Server SEA-DC1.contoso.com
   ```

1. 以下のWindows PowerShellコマンドレットを実行し、**Seattle** OU 配下に、 **Ty Carlson** というユーザーを作成します。

   ```powershell
   New-ADUser -Name Ty -DisplayName 'Ty Carlson' -GivenName Ty -Surname Carlson -Path 'OU=Seattle,DC=contoso,DC=com'
   ```

1. Ty のユーザー アカウントのパスワードを設定するには、次のコマンド実行します。

   ```powershell
   Set-ADAccountPassword Ty
   ```

1. 現在のパスワードの入力を求めるメッセージが表示されたら、何も入力せずにEnter キーを押します。

   ※ユーザーTyの作成時にパスワード設定をせずに作成したため、現在のパスワードは設定されていません。

1. 新たに設定するパスワードを求めるメッセージが表示されたら、「**Pa55w.rd**」と入力し、Enter キーを押します。

1. パスワードをもう一度入力するよう求められたら、「**Pa55w.rd**」と入力し、Enter キーを押します。

1. アカウントを有効にするには、次のコマンドを入力し、Enter キーを押します。

   ```powershell
   Enable-ADAccount Ty
   ```

1. **SEA-SVR1** のドメインコントローラーに **SeattleBranchUsers** という名前のドメイン グローバル グループを作成します。**SEA-ADM1** の **Windows PowerShell (管理者)** で以下のコマンドレットを実行します。

   ```powershell
   New-ADGroup SeattleBranchUsers -Path 'OU=Seattle,DC=contoso,DC=com' -GroupScope Global -GroupCategory Security -Server SEA-SVR1.contoso.com
   ```

1.  以下のWindows PowerShell コマンドレットを実行し、 **SeattleBranchUsers** に ユーザー **Ty**  を追加します。

   ```powershell
   Add-ADGroupMember -Identity SeattleBranchUsers -Members Ty
   ```

1. 以下のWindows PowerShell コマンドレットを実行し、 **SeattleBranchUsers** に ユーザー **Ty** が追加されたことを確認します。

    ```powershell
    Get-ADGroupMember -Identity SeattleBranchUsers
    ```

    ![AZ-800_Lab1_13](./media/AZ-800_Lab1_13.png)

1. 以下のWindows PowerShell コマンドレットを実行し、ローカルの管理者グループ(Administrators グループ)にユーザー Ty を追加します。

    ```powershell
    Add-LocalGroupMember -Group 'Administrators' -Member 'CONTOSO\Ty'
    ```

    > **注**:  **SEA-ADM1** はドメインコントローラーのため、標準ユーザーのTyはサインインできないためです。この次の演習で検証のためにサインインする必要があるため、管理者グループにユーザーを追加しました。

#### **結果**: この演習が完了すると、AD DS に新しいドメイン コントローラーとマネージド オブジェクトが作成できています。

## **オプション : レプリケーションの検証** 

1.  **SEA-ADM1** の **[スタート]** メニューから **[Server Manager]** を起動させます。
2. [Tools]を展開し、**[Active Directory Users and Computers (Active Directory ユーザーとコンピューター)]** を選択します。

![AZ-800_Lab1_14](./media/AZ-800_Lab1_14.png)

3.  **Seattle OU**  配下に、 **SeattleBrunchUsers** グループとユーザー **Ty** が作成されていることが確認できます。

   ![AZ-800_Lab1_15](./media/AZ-800_Lab1_15.png)

4.  **SeattleBrunchUsers** グループのプロパティを確認すると、ユーザー **Ty** がメンバーとして追加されていることが確認できます。

![AZ-800_Lab1_16](./media/AZ-800_Lab1_16.png)

**注 : OUとグループは、リモートサーバーのドメインコントローラーにコマンドで作成しましたが、レプリケーション(複製)されるため、GUIでも確認ができます。** 

## <a name="exercise-2-configuring-group-policy"></a>演習 2: グループ ポリシーの構成

### <a name="task-1-create-and-edit-a-gpo"></a>タスク 1: GPO を作成および編集する

1. **SEA-ADM1** で、サーバー マネージャーから **[Tools(ツール)]** を選択し、**[Group Policy Management (グループ ポリシーの管理)]** を選びます。

   ![AZ-800_Lab1_17](./media/AZ-800_Lab1_17.png)

   

1.  **[Group Policy Management (グループ ポリシーの管理)]** コンソールのナビゲーション ペインで、 **[Forest:Contoso.com] - [Domains] - [Contoso.com]** の順に展開してから、 **[Group Policy Objects (グループ ポリシー オブジェクト)]** コンテナーを選択します。

   ![AZ-800_Lab1_18](./media/AZ-800_Lab1_18.png)

   ※Group Policy Objects コンテナーには、既定でリンク済みのDefault Domain Contorollers Policyと Default Domain Policyが格納されています。

1. ナビゲーション ペインで、**[Group Policy Objects (グループ ポリシー オブジェクト)]** コンテナーのコンテキスト メニューを右クリックして、 **[New (新規)]** を選択します。

   

   ![AZ-800_Lab1_19](./media/AZ-800_Lab1_19.png)

   

1.  **[Name]** テキスト ボックスに「**CONTOSO Standards**」と入力し、**[OK]** をクリックします。

   

   ![AZ-800_Lab1_20](./media/AZ-800_Lab1_20.png)

   

1. **4** の手順で作成した **[CONTOSO Standards]** GPOを右クリックし、 **[Edit (編集)]** をクリックします。

   ![AZ-800_Lab1_21](./media/AZ-800_Lab1_21.png)

   

1.  **[Group Policy Management Editor (グループ ポリシー管理エディター)]** ウィンドウのナビゲーション ペインで、**[User Configuration (ユーザーの構成)] - [Policies (ポリシー)] - [Administrative Templates (管理用テンプレート)]** の順に展開してから **[System (システム)]** をクリックします。

   ![AZ-800_Lab1_22](./media/AZ-800_Lab1_22.png)

1. System のポリシー一覧から、 **[Prevent access to registry editing tools (レジストリ編集ツールへアクセスできないようにする)]** ポリシーをダブルクリックします。

   ![AZ-800_Lab1_23](./media/AZ-800_Lab1_23.png)

1. **[Prevent access to registry editing tools (レジストリ編集ツールへアクセスできないようにする)]** ダイアログ ボックスで、**[Enabled (有効)]**、のラジオボタンをチェックし、 **[OK]** をクリックします。

   

   ![AZ-800_Lab1_24](./media/AZ-800_Lab1_24.png)

   > **※Prevent access to registry editing tools (レジストリ編集ツールへアクセスできないようにする)ポリシーを有効化すると、Windows のレジストリ エディター (Regedit.exe) を無効にします。ユーザーがRegedit.exeを実行しようとすると、「この操作はポリシー設定によって禁止されています」というメッセージが表示されるようになります。**

1. ナビゲーション ペインに戻り、 **[User Configuration (ユーザーの構成)] - [Policies (ポリシー)] - [Administrative Templates (管理用テンプレート)]** の順に展開し、**[Control Panel (コントロール パネル)]** を展開してから **[Personalization (個人用設定)]** をクリックします。

   ![AZ-800_Lab1_25](./media/AZ-800_Lab1_25.png)

1. 詳細ペインで、**[Screen saver timeout (スクリーン セーバーのタイムアウト)]** ポリシー設定をダブルクリックします。

   ![AZ-800_Lab1_26](./media/AZ-800_Lab1_26.png)

1. **[Screen saver timeout (スクリーン セーバーのタイムアウト)]** ダイアログ ボックスで、**[Enabled (有効)]** を選択します。 **[Seconds (秒)]** テキスト ボックスに「**600**」と入力してから、**[OK]** をクリックします。 

   ![AZ-800_Lab1_27](./media/AZ-800_Lab1_27.png)

   > **※Screen saver timeout (スクリーン セーバーのタイムアウト)ポリシーを有効化すると、スクリーンセーバーの起動する時間(秒単位)を指定できます。ラボでは600秒で設定したため、600秒コンピューターでの作業が行われない場合はスクリーンセーバーが起動するようになります。**

   

1. ポリシー一覧に戻り、 **[Password protect the screen saver (パスワードでスクリーン セーバーを保護する)]** ポリシー設定をダブルクリックします。

   ![AZ-800_Lab1_28](./media/AZ-800_Lab1_28.png)

1. **[Password protect the screen saver (スクリーン セーバーをパスワードで保護する)]** ダイアログ ボックスで、**[Enabled (有効)]** を選んでから、**[OK]** をクリックします。

   

   ![AZ-800_Lab1_29](./media/AZ-800_Lab1_29.png)

   > **※[Password protect the screen saver (スクリーン セーバーをパスワードで保護する)] ポリシーを有効にすると、すべてのスクリーン セーバーはパスワードで保護されます。また、有効化することで、コントロール パネルの [個人用設定] の [スクリーン セーバー] ダイアログにある [パスワード保護] チェック ボックスが無効になります。ユーザーはパスワード保護設定を変更できなくなります。**

   

1. 設定したポリシーが **[Enabled]** になっていることを確認し、 **[Group Policy Management Editor (グループ ポリシー管理エディター)]** ウィンドウを × で閉じます。

   ![AZ-800_Lab1_30](./media/AZ-800_Lab1_30.png)

### <a name="task-2-link-the-gpo"></a>タスク 2: GPO をリンクする

1. **[Group Policy Management (グループ ポリシーの管理)]** ウィンドウのナビゲーション ペインで、`Contoso.com` ドメインのコンテキスト メニューを右クリックし、**[Link an Existing GPO (既存の GPO のリンク)]** を選択します。

   ![AZ-800_Lab1_31](./media/AZ-800_Lab1_31.png)

1. **[Select GPO (GPO の選択)]** ダイアログ ボックスで、**[CONTOSO Standards]** を選択してから、**[OK]** をクリックします。

   ![AZ-800_Lab1_32](./media/AZ-800_Lab1_32.png)

   3. Contoso.com ドメインに、 **CONTOSO Standards** と **Default Domain Policy** の2つのGPOがリンクされたことが確認できます。

      ![AZ-800_Lab1_33](./media/AZ-800_Lab1_33.png)

      

### <a name="task-3-review-the-effects-of-the-gpos-settings"></a>タスク 3: GPO の結果を検証する

1. **SEA-ADM1** で、タスク バーの検索ボックスに「**Control Panel**」と入力します。 

1. **[Best match (最も一致する検索結果)]** リストで、**[Control Panel]** を選択します。

1. **[System and Security]** を選択してから、**[Allow an app through Windows Firewall (Windows ファイアウォールによるアプリケーションの許可)]** を選びます。

   ![AZ-800_Lab1_34](./media/AZ-800_Lab1_34.png)

   

   ![AZ-800_Lab1_35](./media/AZ-800_Lab1_35.png)

   

1. **[Allowed apps and features (許可されているアプリと機能)]** リストから、**[Remote Event Log Management (リモート イベント ログ管理)]** エントリを探し、**[Domain]** 列のチェックボックスをオンにしてから、**[OK]** をクリックします。 

   ![AZ-800_Lab1_36](./media/AZ-800_Lab1_36.png)

   

1.  **SEA-ADM1** から一度サインアウトし、以下のユーザーでサインインします。

   | ユーザー名     | CONTOSO\Ty   |
   | -------------- | ------------ |
   | **パスワード** | **Pa55w.rd** |

   ※サインイン画面では **[Other user]** を選択してから資格情報を入力してください。

1. タスク バーの検索ボックスに、「**Control Panel**」と入力して検索します。

1. **[Best match (最も一致する検索結果)]** リストで、**[Control Panel]** を選択します。

1. [コントロール パネル] の検索ボックスに「**Screen Saver**」と入力し、**[change Screen Saver (スクリーン セーバーの変更)]** を選択します  (オプションが表示されるまでに数分かかる場合があります)。

   <img src="./media/AZ-800_Lab1_37.png" alt="AZ-800_Lab1_37"  />

   

1. **[Screen Saver Settings]** ダイアログ ボックスで、**[Wait (待ち時間)]** オプションがグレーアウトされていることが確認できます。また **[On resume, display logon screen (再開時にログオン画面に戻る)]** オプションのチェックボックスにチェックが入り、設定を変更できないことが確認できます。設定を確認したら、ダイアログボックスは **[OK]** をクリックして閉じます。

   ![AZ-800_Lab1_38](./media/AZ-800_Lab1_38.png)

   > **注: Screen saver timeout (スクリーン セーバーのタイムアウト)ポリシーを有効化しているため、ユーザーはスクリーンセーバーの待ち時間等を変更することはできません。**
   >
   > **注: [再開時にログオン画面に戻る] オプションが選択されて淡色表示になっていない場合は、Windows PowerShellを起動し `gpupdate /force` を実行してから再度確認してください。**

1. タスク バーの検索ボックスに、**[regedit]** と入力し、検索結果に表示される **[Registry Editor]** をクリックします。**[Registry editing has been disabled by your administrator (レジストリ編集は、管理者によって使用不可にされています) ]**というエラー メッセージが表示されます。

   ![AZ-800_Lab1_39](./media/AZ-800_Lab1_39.png)

   > **注: Prevent access to registry editing tools (レジストリ編集ツールへアクセスできないようにする)ポリシーを有効化しているため、ユーザーはRegistry Editorを起動させることはできません。**

1. ダイアログ ボックスを **[OK]** をクリックして閉じます。

1.  **SEA-ADM1** からサインアウトし、 **CONTOSO\\Administrator** で再度サインインします。(パスワードは **Pa55w.rd** )

### <a name="task-4-create-and-link-the-required-gpos"></a>オプション1 : GPO を新規作成時し、OUにリンクする

1. **SEA-ADM1** で、Server Managerから **[Tools (ツール)]** を選択し、**[Group Policy Management (グループ ポリシーの管理)]** をクリックします。

1. **[Group Policy Management (グループ ポリシーの管理)]** コンソールのナビゲーション ペインで、 **[Forest:Contoso.com] - [Domains] - [Contoso.com]**  の順に展開してから、**[Seattle]** を選択します。

1. **Seattle OU** のコンテキスト メニューを右クリックしてから、**[Create a GPO in this domain, and Link it here (このドメインに GPO を作成し、このコンテナーにリンクする)]** を選択します。

   ![AZ-800_Lab1_40](./media/AZ-800_Lab1_40.png)

1. **[New GPO]** ダイアログ ボックスの **[Name]** テキスト ボックスに「**Seattle Application Override**」と入力し、**[OK]** を選択します。

   ![AZ-800_Lab1_41](./media/AZ-800_Lab1_41.png)

1.  **Seattle OU** を展開し、 **Seattle Application Override  GPO**  のコンテキスト メニューを右クリックし、**[Edit (編集)]** を選択します。

   ※確認メッセージが表示される場合がありますが、 **[OK]** をクリックして閉じて構いません。

1.  **[Group Policy Management Editor]** が起動したら、**[User Configuration (ユーザーの構成)] - [Policies (ポリシー)] - [Administrative Templates (管理用テンプレート)] - [Control Panel (コントロール パネル)]** を展開してから **[Personalization (個人用設定)]** をクリックします。

1. **[Screen saver timeout (スクリーン セーバーのタイムアウト)]** ポリシー設定をダブルクリックします。

1. **[Disabled (無効)]** を選択してから、**[OK]** をクリックします。

   ![AZ-800_Lab1_42](./media/AZ-800_Lab1_42.png)

   > **注 : Contoso.com ドメインにリンク済みの、 [CONTOSO Standards GPO] では [Screen saver timeout (スクリーン セーバーのタイムアウト)] ポリシーを有効化し、600秒で設定しているため、競合が発生します。**

1. **[Group Policy Management Editor (グループ ポリシー管理エディター)]** ウィンドウを **×** で閉じます。

### <a name="task-5-verify-the-order-of-precedence"></a>オプション 2 : 優先順位を確認する

1. **[Group Policy Management (グループ ポリシーの管理)]**  画面に戻り、**Seattle OU** を確認します。

1. **[Group Policy Inheritance (グループ ポリシーの継承)]** タブを選択し、その内容を確認します。

   ![AZ-800_Lab1_43](media/AZ-800_Lab1_43.png)

   > **注: Seattle Application Override GPO は CONTOSO Standards GPO より優先順位が高くなります。 Seattle Application Override GPO で構成したスクリーン セーバーのタイムアウト ポリシー設定は、CONTOSO Standards GPO の設定の後に適用されます。 そのため、 CONTOSO Standards GPO 設定は上書きされます。 Seattle Application Override GPO のスコープ内のユーザー(Ty Carlson)に対して、スクリーン セーバーのタイムアウトが無効になります。**

   ※余裕があれば、 **SEA-ADM1** からサインアウトし、 **CONTOSO\Ty**  ユーザーでサインインし、コントロールパネルからスクリーンセーバーの設定を確認してみてください。以下のように **[Wait (待ち時間)]** のグレーアウトが解除されていることが確認できます。

   ![AZ-800_Lab1_44](./media/AZ-800_Lab1_44.png)

### <a name="task-6-configure-the-scope-of-a-gpo-with-security-filtering"></a>オプション3: セキュリティ フィルター処理を使用して GPO のスコープを構成する

1. **SEA-ADM1** の **[Group Policy Management (グループ ポリシーの管理)]** コンソールのナビゲーション ペインで、 **Seattle OU** を展開し、**Seattle OU** にリンク済みの **Seattle Application Override GPO** を選択します。

   ![AZ-800_Lab1_45](./media/AZ-800_Lab1_45.png)

   

1. **「You have selected a link to a Group Policy Object (GPO). Except for changes to link properties, changes you make here are global to the GPO, and will impact all other locations where this GPO is linked.」 ([グループ ポリシー管理コンソール] ダイアログ ボックスで、グループ ポリシー オブジェクト (GPO) へのリンクを選択しました。リンク プロパティへの変更以外、ここで行われた変更は GPO にグローバルに適用され、この GPO がリンクされた他の場所すべてに影響します。)というメッセージが表示されたら、 [Do not show this message again(今後このメッセージを表示しない)]**にチェックを入れて  **[OK]** をクリックします。

1. **[Security Filtering (セキュリティ フィルター処理)]** セクションを確認し、**[Authenticated Users (認証されたユーザー)]** に GPO が既定で適用されることが確認できます。

   ![AZ-800_Lab1_46](./media/AZ-800_Lab1_46.png)

1. **[Security Filtering (セキュリティ フィルター処理)]** セクションで、**[Authenticated Users (認証されたユーザー)]** を選んでから **[Remove (削除)]** を選択します。

   ![AZ-800_Lab1_47](./media/AZ-800_Lab1_47.png)

   **※削除を実行しようとすると、以下のような警告メッセージが表示されますが、そのまま [OK]をクリックします。**

   ![AZ-800_Lab1_48](./media/AZ-800_Lab1_48.png)

1.  更に、以下のような警告メッセージを確認してからもう一度 **[OK]** をクリックします。

   ![AZ-800_Lab1_49](./media/AZ-800_Lab1_49.png)

   > **注: 警告メッセージは、「グループ ポリシーでは、ユーザーの GPO 設定を正常に適用するために、各コンピューター アカウントにドメイン コントローラーから GPO データを読み取るアクセス許可が求められます。 GPO のセキュリティ フィルター処理設定を変更する場合は、注意してください。」と表示されています。**

1. **[Security Filtering (セキュリティ フィルター処理)]** セクションで、**[Add (追加)]** をクリックします。

   ![AZ-800_Lab1_50](./media/AZ-800_Lab1_50.png)

1. **[Select User, Computer, or Group (ユーザー、コンピューター、またはグループの選択)]** ダイアログ ボックスで、**[Enter the object name to select (選択するオブジェクト名を入力してください)]** テキスト ボックスに「**SeattleBranchUsers**」と入力してから、**[OK]** をクリックします。

   ![AZ-800_Lab1_51](./media/AZ-800_Lab1_51.png)

1.  **[Security Filtering (セキュリティ フィルター処理)]** セクションに戻り、再度 **[Add (追加)]** をクリックします。

1. **[Select User, Computer, or Group (ユーザー、コンピューター、またはグループの選択)]** ダイアログ ボックスで、**[Object Types (オブジェクトの種類)]** をクリックします。

   ![AZ-800_Lab1_52](./media/AZ-800_Lab1_52.png)

1. **[Object Types (オブジェクトの種類)]** ダイアログ ボックスで、**[Computers]** のチェックボックスをオンにしてから、**[OK]** をクリックします。

   ![AZ-800_Lab1_53](./media/AZ-800_Lab1_53.png)

1. **[Select User, Computer, or Group (ユーザー、コンピューター、またはグループの選択)]** ダイアログ ボックスで、**[Enter the object name to select (選択するオブジェクト名を入力してください)]** ボックスに「**SEA-ADM1**」と入力してから **[OK]** をクリックします。

   ![AZ-800_Lab1_54](./media/AZ-800_Lab1_54.png)

   ※Security Filtering に、コンピューターオブジェクトのSEA-ADM1とグループオブジェクトのSaettleBrunchUsersグループが追加されました。次のオプションの手順で必要なフィルター設定です。

### <a name="task-7-verify-the-application-of-settings"></a>オプション4: 設定のシミュレーション結果を確認する

1.  **[Group Policy Management (グループ ポリシーの管理)]** で、**[Group Policy Modeling (グループ ポリシーのモデル作成)]** を選択します。

   ![AZ-800_Lab1_55](./media/AZ-800_Lab1_55.png)

1. **[Group Policy Modeling (グループ ポリシーのモデル作成)]** のコンテキスト メニューを右クリックし、**[Group Policy Modeling Wizard (グループ ポリシーのモデル作成ウィザード)]** を選択します。

   ![AZ-800_Lab1_56](./media/AZ-800_Lab1_56.png)

1. **[Group Policy Modeling Wizard (グループ ポリシーのモデル作成ウィザード)]**  画面では、**[Next (次へ)]** をクリックします。

1. **[Domain Controller Selection (ドメイン コントローラーの選択)]** 画面では、既定の設定のまま、**[Next  (次へ)]** をクリックします。

   ![AZ-800_Lab1_57](./media/AZ-800_Lab1_57.png)

1. **[User and Computer Selection (ユーザーとコンピューターの選択)]** 画面の **[User information (ユーザー情報)]** セクションで、**[User]** を選択してから、テキスト ボックスに「**CONTOSO\Ty**」と入力するか、**[Browse (参照)]** ボタンを使用して **Ty** ユーザー アカウントを検索します。

   ![AZ-800_Lab1_58](./media/AZ-800_Lab1_58.png)

1. **[User and Computer Selection ユーザーとコンピューターの選択]** ページの **[Computer information (コンピューター情報)]** セクションで、**[Computer]** を選択し、テキスト ボックスに「**CONTOSO\SEA-ADM1**」と入力したあと、 **[Next (次へ)]** をクリックします。

   ![AZ-800_Lab1_59](./media/AZ-800_Lab1_59.png)

   

1. **[Advanced Simulation Options (詳細シミュレーション オプション)]** 画面では、既定の設定のまま、**[Next (次へ)]** をクリックします。

   ![AZ-800_Lab1_60](./media/AZ-800_Lab1_60.png)

1. **[Alternate Active Directory Paths (代替 Active Directory パス)]** 画面の、ユーザーとコンピューターのロケーションを確認したら、 **[次へ]** をクリックします。(ユーザーロケーションはSeattle OU、コンピューターロケーションは Computers コンテナーが選択されているはずです。)

   ![AZ-800_Lab1_61](./media/AZ-800_Lab1_61.png)

1. **[User Security Groups (ユーザー セキュリティ グループ)]** 画面で、グループのリストに **CONTOSO\\ SeattleBranchUsers** が含まれていることを確認してから、 **[Skip to the final page of this wizard without collecting additional data. (追加データを収集せずに、このウィザードの最終ページまでスキップします。)]**  のチェックボックスにチェックを入れ **[Next (次へ)]** を選択します。

   ![AZ-800_Lab1_62](./media/AZ-800_Lab1_62.png)

   

1. **[Summary of Selections (選択の要約)]** 画面で、**[Next (次へ)]** をクリックします。

1. メッセージが表示されたら、**[Finish (完了)]** をクリックします。

1. Group Policy Modeling に作成されたレポートの詳細ペインで、**[Details (詳細)]** タブを選択し、**[show all (すべて表示)]** を選択します。

   ![AZ-800_Lab1_63](./media/AZ-800_Lab1_63.png)

1. レポートで、 **[User Details (ユーザーの詳細)]** セクションが見つかるまで下にスクロールし、 **[Control Panel/Personalization (コントロールパネル/個人用設定)]** セクションを確認します。 **[Screen saver timeout]** 設定が無効になっており、優勢な GPO がSeattle Application Override GPO に設定されていることが確認できます。

   ![AZ-800_Lab1_64](./media/AZ-800_Lab1_64.png)

1. **[Group Policy Management (グループ ポリシーの管理)]** コンソールを閉じます。

**結果**: この演習が完了すると、GPO を正常に作成して構成し、結果を確認できたことになります。
