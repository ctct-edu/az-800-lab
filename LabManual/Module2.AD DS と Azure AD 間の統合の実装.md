---
lab:
  title: 'ラボ: AD DS と Azure AD の統合の実装'
  module: 'Module 2: Implementing Identity in Hybrid Scenarios'
---

# <a name="lab-implementing-integration-between-ad-ds-and-azure-ad"></a>ラボ: AD DS と Azure AD の統合の実装

## <a name="scenario"></a>シナリオ

Microsoft Azure Active Directory (Azure AD) を使用して Azure リソースへのアクセスを認証および承認したことで生じる管理と監視のオーバーヘッドについての懸念に対応するために、あなたは、オンプレミスの Active Directory Domain Services (AD DS) と Azure AD の間の統合をテストし、複数のユーザー アカウントの管理に、オンプレミスとクラウド リソースを組み合わせて使用することに関するビジネス上の懸念に対処できることを検証することにしました。

さらに、情報セキュリティ チームの懸念事項に対応し、サインイン時間やパスワード ポリシーなどの、Active Directory ユーザーに適用される既存のルールを保持することを確認する必要がります。 そこで、オンプレミスの Active Directory のセキュリティを一層強化し、管理オーバーヘッドを最小限に抑える Azure AD 統合機能を検証することとなります。検証には、Windows Server Active Directory 用の Azure AD パスワード保護や、パスワード ライトバックを使用したセルフサービス パスワード リセット (SSPR) が含まれます。

このラボの目標は、オンプレミスの AD DS と Azure AD の間でパススルー認証を実装することです。

## <a name="objectives"></a>目標

このラボを完了すると、次のことができるようになります。

- カスタム ドメインの追加や検証を含め、Azure AD とオンプレミス AD DS との統合の準備を行う。
- IdFix DirSync エラー修復ツールの実行を含め、オンプレミス AD DS と Azure AD の統合の準備を行う。
- Azure AD Connect をインストールし、構成する。
- 同期プロセスをテストして、AD DS とAzure AD の統合を検証する。
- Active Directory に Azure AD 統合機能を実装する。これには Windows Server Active Directory 用の Azure AD パスワード保護や、パスワード ライトバックを使用した SSPR が含まれます。

## <a name="estimated-time-60-minutes"></a>予想所要時間: 60 分

## <a name="lab-setup"></a>ラボのセットアップ

使用する仮想マシン: **AZ-800T00A-SEA-DC1**、**AZ-800T00A-SEA-SVR1**、**AZ-800T00A-ADM1**  

> **注**: **AZ-800T00A-SEA-DC1**、**AZ-800T00A-SEA-SVR1**、**AZ-800T00A-SEA-ADM1** 仮想マシンによって、**SEA-DC1**、**SEA-SVR1**、**SEA-ADM1** のインストールがホストされます。

1. **SEA-ADM1** を選択します。
1. **SEA-ADM1** に次の資格情報を使用してサインインします。
   - ユーザー名: **Administrator**
   - パスワード: **Pa55w.rd**
   - ドメイン: **CONTOSO**

このラボでは、使用可能な VM 環境と Azure AD テナントを使用します。 ラボを開始する前に、Azure AD テナントと、そのテナントのグローバル管理者ロールがあるユーザー アカウントを持っていることを確認します。

## <a name="exercise-1-preparing-azure-ad-for-ad-ds-integration"></a>演習 1: AD DS 統合のための Azure AD の準備

### <a name="scenario"></a>シナリオ

ラボ内のAzure AD 環境で、オンプレミスの AD DS と統合する準備ができている必要があります。 カスタムAzure AD ドメイン名とグローバル管理者ロールのあるアカウントを作成して検証します。

この演習の主なタスクは次のとおりです。

1. Azure でカスタム ドメイン名を作成する。
1. グローバル管理者ロールを持つユーザーを作成する。
1. グローバル管理者ロールを持つユーザーのパスワードを変更する。

### <a name="task-1-create-a-custom-domain-name-in-azure"></a>タスク 1: Azure でカスタム ドメイン名を作成する。

1. **SEA-ADM1** で Microsoft Edge を起動してから、Azure Portal ( https://portal.azure.com )に移動します。

1. ラボで提供されている資格情報を使用して、Azure portal にサインインします。

   

   ![AZ-800_Lab2_01](./media/AZ-800_Lab2_01.png)

   **※ログイン時に 「Do this to reduce the number of times you are asked to sign in. (サインインの状態を維持すると、次回もう一度サインインする必要がなくなります。)」というメッセージが表示されますが、[Yes]、[NO]のどちらでも構いません。[NO]を選択すると、毎回認証が求められます。**

   ![AZ-800_Lab2_02](./media/AZ-800_Lab2_02.png)

1. Azure Portalにログインが成功すると、「Welcome to Microsoft Azure」のメッセージが表示されます。「Maybe later」をクリックしツアーを終了させます。

   ![AZ-800_Lab2_03](./media/AZ-800_Lab2_03.png)

   

   **※以下の手順でAzure Portal の言語設定を日本語に変更することができます。**

   1. Azure Portal にログイン後、画面右上の歯車マークをクリックします。

   ![AZ-800_Lab2_04](./media/AZ-800_Lab2_04.png)

   2. **[Portal settings | Directories + subscriptions]** 画面の左ナビゲーションペインから **[Language + region]** を選択します。

   ![AZ-800_Lab2_05](./media/AZ-800_Lab2_05.png)

   3. **[Language]** のプルダウンから **[日本語]** を選択し、 **[Apply]** をクリックします。( **「Change language」** の確認メッセージが表示されたら **[OK]** を選択してください。)

      ![AZ-800_Lab2_06](./media/AZ-800_Lab2_06.png)

   4. Azure Portal のメニューが日本語表記に変更されます。

      **注: Azure Portal内のすべてのメニューに日本語が適用されるまで、数時間要する場合があります。**

      

1. Azure portal の検索ボックス内に、**[Azure Active Directory]** と入力します。

1. 検索結果の [サービス] から **[Azure Active Directory]** を選択します。

   ![AZ-800_Lab2_07](./media/AZ-800_Lab2_07.png)

1.  [Azure Active Directory] の左ナビゲーションペインの一覧から**[カスタム ドメイン名]** を選択します。

   ![AZ-800_Lab2_08](./media/AZ-800_Lab2_08.png)

1.  **[+ カスタムドメインの追加]** をクリックし、以下のカスタムドメイン名を入力します。

   | カスタムドメイン名 | contoso.com |
   | ------------------ | ----------- |

   カスタムドメイン名を入力したら、 **[ドメインの追加]** をクリックします。

1. ドメインの検証に使用する DNS レコードの種類を確認したら、**[確認] はクリックせず**にそのままウィンドウを閉じます。

   ![AZ-800_Lab2_09](./media/AZ-800_Lab2_09.png)

   > **注**: 一般には、DNS レコードを使用してドメインを確認しますが、このラボでは検証済みドメインを使用しません。



## <a name="exercise-2-preparing-on-premises-ad-ds-for-azure-ad-integration"></a>演習 2: Azure AD 統合のためのオンプレミス AD DS の準備

### <a name="scenario"></a>シナリオ

あなたは、既存の Active Directory 環境で Azure AD との統合の準備ができていることを確認する必要があります。 そのため、IdFix ツールを実行し、Active Directory ユーザーの UPN が Azure AD テナントのカスタム ドメイン名と一致することを確保します。

この演習の主なタスクは次のとおりです。

1. IdFix をインストールする。
1. IdFix を実行する。



### **事前準備 : ContosoAdminの [Display Name 属性] を確認する。**

1.  **SEA-ADM1** にCONTOSO\Administrator でサインインしている状態で、 **[Server Manager]** を起動し、 **[Tools (ツール)] - [Active Directory Users and Computers (Active Directory ユーザーとコンピューター)]** を選択します。

2.   **[Contoso.com] - [Users]** の順に展開し、「 **ContosoAdmin** 」をダブルクリックしてプロパティを確認します。

   ![AZ-800_Lab2_17](./media/AZ-800_Lab2_17.png)

3.  **[General(全般)]** タブの **[Display Name (表示名)]** の属性が空欄になっていることを確認したら、 [OK] をクリックしてプロパティを閉じます。

   ![AZ-800_Lab2_18](./media/AZ-800_Lab2_18.png)

### <a name="task-1-install-idfix"></a>タスク 1: IdFix をインストールする

1. **SEA-ADM1** に CONTOSO\Administraor でサインインし Microsoft Edge を起動します。 Microsoft Edge が起動したら、**https://github.com/microsoft/idfix** にアクセスします。

1. **Github** ページを下にスクロールし、 **[ClickOnce Launch]** で、**[launch]** のリンク をクリックします。

   ![AZ-800_Lab2_10](./media/AZ-800_Lab2_10.png)

1. Microsoft Edge のステータスバーに表示される **[Open file]** をクリックします。

   ![AZ-800_Lab2_11](./media/AZ-800_Lab2_11.png)

1. [Application Install - Security Warning] ダイアログボックスで [Install] をクリックします。

   ![AZ-800_Lab2_12](./media/AZ-800_Lab2_12.png)

1. **[ IdFix Privacy Statement ]** ダイアログ ボックスで、免責事項を確認し、**[OK]** を選択します。

   ![AZ-800_Lab2_13](./media/AZ-800_Lab2_13.png)

### <a name="task-2-run-idfix"></a>タスク 2: IdFix を実行する

1. **IdFix** が起動したら、ウィンドウから **[Query]** を選択します。

   ![AZ-800_Lab2_14](./media/AZ-800_Lab2_14.png)

   **※ [Schema Warning] の警告ダイアログボックスが表示された場合は、 [Yes] をクリックして閉じてください。** 

1. オンプレミス Active Directory のオブジェクトの一覧で **[ERROR]** 列 および **[ATTRIBUTE (属性)]** 列を確認します。 

   ![AZ-800_Lab2_15](./media/AZ-800_Lab2_15.png)

   > **注 :** このシナリオでは、**ContosoAdmin** の **displayName** の値が **空白 (Blank)** となっており、ツールが推奨する値 (ContosoAdmin) が **[UPDATE]** 列に表示されます。
   >
   > **注 : ERROR 列の Blank は、必要な個所(DisplayName) に値が入力されていないことを意味します。** 事前準備で確認した通り、ContosoAdminのDisplayName 属性は未入力の状態のため、入力を促されるエラーが表示されました。

   

1. **IdFix** ウィンドウの、**[ACTION] **ドロップダウン メニューで **[Edit (編集)]** を選択し、**[Apply (適用]** をクリックすると、推奨される値の変更が自動的に実装されます。

   ![AZ-800_Lab2_16](./media/AZ-800_Lab2_16.png)

1. **[Apply Pending (保留中の適用)]** ダイアログボックスが表示されたら、 **[Yes]** をクリックします。

   ![AZ-800_Lab2_19](./media/AZ-800_Lab2_19.png)

1. **IdFix** ウィンドウの、 **[ACTION]** ドロップダウン メニューが **[COMPLETE]** に変更されたことを確認し、ウィンドウを × で閉じます。

   ![AZ-800_Lab2_20](./media/AZ-800_Lab2_20.png)

1. **[Active Directory Users and Computers (Active Directory ユーザーとコンピューター)]** に戻り、ContosoAdminのプロパティを再度確認すると、IdFixツールで更新した、**[Display Name (表示名)]** 属性に **「ContosoAdmin」** と入力されていることが確認できます。確認できたら、**[OK]** をクリックしてプロパティと、 **[Active Directory Users and Computers (Active Directory ユーザーとコンピューター)]** を閉じます。

   ![AZ-800_Lab2_21](./media/AZ-800_Lab2_21.png)

## <a name="exercise-3-downloading-installing-and-configuring-azure-ad-connect"></a>演習 3: Azure AD Connect のダウンロード、インストール、構成

### <a name="scenario"></a>シナリオ

演習のシナリオ: Azure AD Connect をダウンロードし、**SEA-ADM1** にインストールし、設定を構成します。

この演習の主なタスクは次のとおりです。

1. Azure AD Connect をインストールし、構成する。

### <a name="task-1-install-and-configure-azure-ad-connect"></a>タスク 1: Azure AD Connect のインストールと構成

1. **SEA-ADM1** で Microsoft Edge を起動し 、Azure Portal (https://portal.azure.com) にログインします。 Azure Portal の検索ボックスで **[Azure Active Directory]** を検索し、左のナビゲーションペインの一覧から  **[Azure AD Connect]** を参照します。

   ![AZ-800_Lab2_22](./media/AZ-800_Lab2_22.png)

1. **[Azure AD Connect]** ページから、**[Azure AD Connect のダウンロード]** を選択します。

   ![AZ-800_Lab2_23](./media/AZ-800_Lab2_23.png)

1.  **[Microsoft Azure Active Directory Connect]** ページで、 **[Download]** をクリックします。

   ![AZ-800_Lab2_24](./media/AZ-800_Lab2_24.png)

1. ステータス バーで、**[AzureAD Connect.msi]** ファイルの  **[Open file]** を選択します。

   ![AZ-800_Lab2_25](./media/AZ-800_Lab2_25.png)

1. Azure AD Connect インストール バイナリーをダウンロードし、インストールを開始します。

1. **[Welcome to Azure AD Connect]** 画面で、**[ I agree to the license terms and privacy notice (ライセンス条項とプライバシーに関する声明に同意します)]** チェックボックスをオンにし、**[Continue (続行)]** を選択します。

   ![AZ-800_Lab2_26](./media/AZ-800_Lab2_26.png)

1. **[Express Settings (簡易設定)]** ページで、**[Use express settings (簡易設定を使用する)]** をクリックします。

   ![AZ-800_Lab2_27](./media/AZ-800_Lab2_27.png)

1. **[Connect to Azure AD (Azure AD への接続)]** 画面で、Azure AD のグローバル管理者アカウントの資格情報を入力、 **[Next (次へ)]** をクリックします。(資格情報はラボの [Home] 上で提供されているものを使用してください。)

   ![AZ-800_Lab2_28](./media/AZ-800_Lab2_28.png)

1. **[Connect to AD DS (AD DS への接続)]** ページで、次の資格情報を入力し、 **[Next (次へ)]** をクリックします。

   | USERNAME (ユーザー名)     | CONTOSO\\Administrator |
   | ------------------------- | ---------------------- |
   | **PASSWORD (パスワード)** | **Pa55w.rd**           |

1. **[Azure AD sign-in configuration (Azure AD サインイン構成)]** ページで、追加した新しいドメイン  **(contoso.com)** が Active Directory UPN サフィックスの一覧に含まれていることを確認します。

   ![AZ-800_Lab2_29](./media/AZ-800_Lab2_29.png)

   > **注**:追加したドメインは、 演習1のタスク1で追加したカスタムドメイン名です。状態が [Not Verified (未確認)] と表示されますが、検証済みドメインである必要はありません。 尚、通常は Azure AD Connect をインストールする前にドメインの検証しますが、このラボでは検証手順は不要です。カスタムドメイン名の検証については、ドメインレジストラーへのDNSのレコードの登録等が必要です。詳細は [参考URL](https://learn.microsoft.com/ja-jp/azure/active-directory/fundamentals/add-custom-domain#add-your-dns-information-to-the-domain-registrar) をご参照ください。

1. **[Continue without matching all UPN suffixes to verified domains (一部の UPN サフィックスが検証済みドメインに一致していなくても続行する)]** チェックボックスをオンにして、 **[Next (次へ)]** をクリックします。

   ![AZ-800_Lab2_30](./media/AZ-800_Lab2_30.png)

1. **[Ready to configure (構成の準備完了)]** 画面が表示された後、アクションの一覧を確認し、**[Start the synchronaization process when configuration completes. (構成が完了したら、同期プロセスを開始します。)]** のチェックボックスにチェックが入っていることを確認してから、インストールを開始します。

   **※インストールが完了するまでに5分程度要します。**

   ![AZ-800_Lab2_31](./media/AZ-800_Lab2_31.png)

1.  **[Configuration complete (設定完了)]** 画面が表示されたら、 **[Exit]** をクリックして終了します。

   

## <a name="exercise-4-verifying-integration-between-ad-ds-and-azure-ad"></a>演習 4: AD DS と Azure AD の統合の検証

### <a name="scenario"></a>シナリオ

Azure AD Connect をインストールして構成したので、同期のメカニズムを確認できるようになりました。 あなたは、同期をトリガーするオンプレミスのユーザー アカウントに変更を加える予定です。 次に、対応する Azure AD ユーザー オブジェクトに変更がレプリケートされていることを確認します。

この演習の主なタスクは次のとおりです。

1. Azure portal で同期を検証する。
1. Synchronization Service Manager で同期を検証する。
1. Active Directory でユーザー アカウントを更新する。
1. Active Directory でユーザー アカウントを作成する。
1. Azure AD に対する変更を同期する。
1. Azure AD での変更を検証する。

### <a name="task-1-verify-synchronization-in-the-azure-portal"></a>タスク 1: Azure portal で同期を検証する

1. **SEA-ADM1** で、Azure portal が表示されている Microsoft Edge ウィンドウに切り替えます。 

1. **Azure AD Connect** ページを更新し、**[Active Directory からのプロビジョニング]** の下の情報の **[同期状態]** が **[有効]** となっていることを確認します。※有効が確認できない場合は、ブラウザ更新を実行してください。

   ![AZ-800_Lab2_32](./media/AZ-800_Lab2_32.png)

1. 左のナビゲーションペインから **[ユーザー]** を選択します。

   ![AZ-800_Lab2_33](./media/AZ-800_Lab2_33.png)

1. Active Directory の Contoso.com ドメインから同期されたユーザーの一覧が表示されます。

   > **注**: ディレクトリ同期が開始されると、Active Directory オブジェクトが Azure AD Portalに表示されるまでに 15 分以上要する場合があります。

1. Azure Active Directory に戻り、左ナビゲーションペインの一覧から**[グループ]** を参照します。

   ![AZ-800_Lab2_34](./media/AZ-800_Lab2_34.png)

1. Active Directory の Contoso.com ドメインから同期されたグループの一覧を確認します。

   注: オンプレミスドメインの OU が、Azure AD のグループとして同期されていることが確認できます。(例 : Sales OU、Research OU などがセキュリティグループとして同期されています。)

### <a name="task-2-verify-synchronization-in-the-synchronization-service-manager"></a>タスク 2: Synchronization Service Manager で同期を検証する

1. **SEA-ADM1** の **[スタート]** メニューで、**[Azure AD Connect]** を展開し、**[Synchronization Service (同期サービス)]** を選択します。

   ![AZ-800_Lab2_35](./media/AZ-800_Lab2_35.png)

1. **[Synchronization Service Manager]** ウィンドウの **[Operations (操作)]** タブで、Active Directory オブジェクトを同期するために実行されたタスクが確認できます。

   ![AZ-800_Lab2_36](./media/AZ-800_Lab2_36.png)

1. **[Connectors (コネクタ)]** タブを選択し、2 つのコネクタが存在することを確認します。

   ![AZ-800_Lab2_37](./media/AZ-800_Lab2_37.png)

   > **注**: 1 つのコネクタが AD DS 用で、もう 1 つは Azure AD テナント用です。 

1. コネクタを確認したら、 **[Synchronization Service Manager]** ウィンドウを閉じます。

### <a name="task-3-update-a-user-account-in-active-directory"></a>タスク 3: Active Directory でユーザー アカウントを更新する

1. **SEA-ADM1** の **[Server Manager (サーバー マネージャー)]** で  **[Active Directory Users and Computers (Active Directory ユーザーとコンピューター)]** を起動します。

1. **[Active Directory ユーザーとコンピューター]** で、**Sales** 組織単位 (OU) を展開し、ユーザーの **Ben Miller** をダブルクリック、もしくは右クリックをしてプロパティを表示させます。

   ![AZ-800_Lab2_38](./media/AZ-800_Lab2_38.png)

1. ユーザーのプロパティで、 **[Organization (組織)]** タブを選択します。

1. **[Job Title (役職)]** テキストボックスに「**Manager**」と入力し、**[OK]** をクリックします。

   ![AZ-800_Lab2_39](./media/AZ-800_Lab2_39.png)

### <a name="task-4-create-a-user-account-in-active-directory"></a>タスク 4: Active Directory でユーザー アカウントを作成する

1.   **SEA-ADM1** の **[Server Manager (サーバー マネージャー)]** で  **[Active Directory Users and Computers (Active Directory ユーザーとコンピューター)]** を起動します。

2.  Sales OU を右クリックして、 **[New] - [User]** の順に選択します。

   ![AZ-800_Lab2_40](./media/AZ-800_Lab2_40.png)

3. [New Object - User] ダイアログボックスに以下を入力して、 **[Next]** をクリックします。

   | First name          | Jordan       |
   | ------------------- | ------------ |
   | **Last name**       | **Mitchell** |
   | **User logon name** | **Jordan**   |

   ![AZ-800_Lab2_41](./media/AZ-800_Lab2_41.png)

4. パスワードを **Pa55w.rd** と設定して **[Next]** をクリックします。

   ![AZ-800_Lab2_42](./media/AZ-800_Lab2_42.png)

5. 作成したら、 **[Finish]** をクリックしてダイアログボックスを閉じます。

### <a name="task-5-sync-changes-to-azure-ad"></a>タスク 5: Azure AD に対する変更を同期する

1. **SEA-ADM1** で、管理者として **Windows PowerShell** を起動します。
1. **Windows PowerShell** コンソールで、次のコマンドレットを実行し、Contoso.com ドメインで変更したユーザー情報や作成したユーザーアカウントを同期します。

   ```powershell
   Start-ADSyncSyncCycle
   ```

   > **注**: 同期サイクルの開始後、Active Directory オブジェクトが Azure AD ポータルに表示されるまでに 15 分かかることがあります。

3. 実行結果に [Success] と返ってきたら、Windows PowerShell コンソールを × で閉じて終了します。

### <a name="task-6-verify-changes-in-azure-ad"></a>タスク 6: Azure AD での変更を検証する

1. **SEA-ADM1** で、Microsoft Edge ウィンドウに切り替えて Azure portal を表示し、**[Azure Active Directory]** ページに戻ります。

1. **[Azure Active Directory]** ページから **[ユーザー]** ページを参照します。

1. **[すべてのユーザー]** ページで、ユーザー **Ben Miller** を検索します。

   ![AZ-800_Lab2_43](./media/AZ-800_Lab2_43.png)

1. ユーザー **Ben Miller** のプロパティ ページを開き、**役職**の属性が Active Directory から同期されたことを確認します。※Ben Miller の役職 [Manager] が同期されます。

   ![AZ-800_Lab2_44](./media/AZ-800_Lab2_44.png)

1. Microsoft Edge で、 **[すべてのユーザー]** ページに戻ります。

1. **[すべてのユーザー]** ページで、ユーザーを **Jordan** 検索します。

   ![AZ-800_Lab2_45](./media/AZ-800_Lab2_45.png)

1. Active Directory で作成した、Jordan Mitchell が同期されていることが確認できます。

## <a name="exercise-5-implementing-azure-ad-integration-features-in-ad-ds"></a>演習 5 AD DS での Azure AD 統合機能の実装

### <a name="scenario"></a>シナリオ

オンプレミスの Azure Active Directory のセキュリティをさらに強化し、管理オーバーヘッドを最小限に抑えることができる Azure AD 統合機能を特定する必要があります。 また、Windows Server Active Directory 用の Azure AD パスワード保護と、パスワード ライトバックを使用するセルフサービス パスワード リセットを実装することも希望しています。

この演習の主なタスクは次のとおりです。

1. Azure でセルフサービス パスワード リセットを有効にする。
1. Azure AD Connect でパスワード ライトバックを有効にする。
1. Azure AD Connect でパススルー認証を有効にする。
1. Azure でパススルー認証を検証する。
1. Azure AD パスワード保護プロキシ サービスと DC エージェントをインストールして登録する。
1. Azure でパスワード保護を有効にする。

#### <a name="task-1-enable-self-service-password-reset-in-azure"></a>タスク 1: Azure でセルフサービス パスワード リセットを有効にする

1. **SEA-ADM1** の、Azure portal が表示されている Microsoft Edge ウィンドウで、Azure AD の **[ライセンス]** ページを参照し、**[Azure AD Premium P2]** 無料試用版をアクティブにします。 
1. 演習 1 で作成した Azure AD グローバル管理者ユーザー アカウントに Azure AD Premium P2 ライセンスを割り当てます。
1. Azure portal で、Azure AD の **[パスワードのリセット]** ページを参照します。
1. **[パスワードのリセット]** ページで、構成を適用するユーザーのスコープを選択できることに注目してください。

   > **注**: パスワード リセット機能は、このラボの後半で必要な構成手順が壊れるので、有効にしないでください。

#### <a name="task-2-enable-password-writeback-in-azure-ad-connect"></a>タスク 2: Azure AD Connect でパスワード ライトバックを有効にする

1. **SEA-ADM1** で、**Azure AD Connect** を開きます。
1. **[Microsoft Azure Active Directory Connect]** ウィンドウで、**[構成]** を選択します。
1. **[追加のタスク]** ページで、**[同期オプションのカスタマイズ]** を選択します。
1. **[Azure AD への接続]** ページで、演習 1 で作成した Azure AD グローバル管理者ユーザー アカウントのユーザー名とパスワードを入力します。
1. **[オプション機能]** ページで、**[パスワード ライトバック]** を選択します。

   > **注**: Active Directory ユーザーのセルフサービス パスワード リセットには、パスワード ライトバックが必要です。 これにより、Azure AD 内のユーザーによってパスワードが変更され、Active Directory に同期されます。

1. **[構成の準備完了]** ページで、実行するアクションの一覧を確認し、**[構成]** を選択します。
1. 構成が完了したら、**[Microsoft Azure Active Directory Connect]** ウィンドウを閉じます。

#### <a name="task-3-enable-pass-through-authentication-in-azure-ad-connect"></a>タスク 3: Azure AD Connect でパススルー認証を有効にする

1. **SEA-ADM1** の **[スタート]** メニューで、**[Azure AD Connect]** を展開し、**[Azure AD Connect]** を選択します。
1. **[Microsoft Azure Active Directory Connect]** ウィンドウで、**[構成]** を選択します。
1. **[追加のタスク]** ページで、**[ユーザー サインインの変更]** を選択します。
1. **[Azure AD への接続]** ページで、演習 1 で作成した Azure AD グローバル管理者ユーザー アカウントのユーザー名とパスワードを入力します。
1. **[ユーザー サインイン]** ページで、**[パススルー認証]** を選択します。
1. **[シングル サインオンを有効にする]** チェック ボックスがオンになっていることを確認します。
1. **[シングル サインオンを有効にする]** ページで、**[資格情報の入力]** を選択します。
1. **[フォレストの資格情報]** ダイアログ ボックスで、次の資格情報を使用して認証します。

   - ユーザー名: **Administrator**
   - パスワード: **Pa55w.rd**

1. **[シングル サインオンを有効にする]** ページで、**[資格情報の入力]** の横に緑色のチェック マークが表示されているのを確認します。
1. **[構成の準備完了]** ページで、実行するアクションの一覧を確認し、**[構成]** を選択します。
1. 構成が完了したら、**[Microsoft Azure Active Directory Connect]** ウィンドウを閉じます。

#### <a name="task-4-verify-pass-through-authentication-in-azure"></a>タスク 4: Azure でパススルー認証を検証する

1. **SEA-ADM1** の、Azure portal で **[Azure Active Directory]** ページから **[Azure AD Connect]** ページを参照します。
1. **[Azure AD Connect]** ページの、**[ユーザー サインイン]** の下に表示されている情報を確認します。
1. **[ユーザー サインイン]** で、**[シームレス シングル サインオン]** を選択します。
1. **[シームレス シングル サインオン]** ページで、オンプレミスのドメイン名を確認します。
1. **[シームレス シングル サインオン]** ページから、 **[パススルー認証]** ページを参照します。
1. **[パススルー認証]** ページで、 **[認証エージェント]** の下にあるサーバーのリストを確認します。

   > **注**: Azure AD Authentication エージェントを環境内の複数のサーバーにインストールするには、Azure portal の **[パススルー認証]** ページからバイナリーをダウンロードします。

#### <a name="task-5-install-and-register-the-azure-ad-password-protection-proxy-service-and-dc-agent"></a>タスク 5: Azure AD パスワード保護プロキシ サービスと DC エージェントをインストールして登録する

1. **SEA-ADM1** で Microsoft Edge を起動し、Microsoft ダウンロード Web サイトを参照し、インストーラーをダウンロードできる「**Windows Server Active Directory 用 Azure AD パスワード保護**」のページを参照して、**[ダウンロード]** を選択します。
1. **AzureADPasswordProtectionProxySetup.exe** と **AzureADPasswordProtectionDCAgentSetup.msi** を **SEA-ADM1** にダウンロードします。

   > **注**: ドメイン コントローラーではないサーバーにプロキシ サービスをインストールすることをお勧めします。 さらに、プロキシ サービスは、Azure AD Connect エージェントと同じサーバーにはインストールできません。 プロキシ サービスは **SEA-SVR1** に、パスワード保護 DC エージェントは **SEA-DC1** にインストールします。

1. **SEA-ADM1** の **Windows PowerShell** コンソールで、次のコマンドを入力し、インターネットからファイルがダウンロードされたことを示す Zone.Identifier 代替データ ストリームを削除します。

   ```powershell
   Get-ChildItem -Path "$env:USERPROFILE\Downloads -File | Unblock-File
   ```
1. 次のコマンドを実行して **SEA-SVR1** に **C:\Temp** ディレクトリを作成し、**AzureADPasswordProtectionProxySetup.exe** インストーラーをそのディレクトリにコピーして、インストールを起動します。

   ```powershell
   New-Item -Type Directory -Path '\\SEA-SVR1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionProxySetup.exe" -Destination '\\SEA-SVR1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-SVR1.contoso.com -ScriptBlock { Start-Process -FilePath C:\Temp\AzureADPasswordProtectionProxySetup.exe -ArgumentList `/quiet /log C:\Temp\AzureADPPProxyInstall.log` -Wait }
   ```
1. 次のコマンドを実行して **SEA-DC1** に **C:\Temp** ディレクトリを作成し、**AzureADPasswordProtectionDCAgentSetup.msi** インストーラーをそのディレクトリにコピーし、インストールを起動して、インストールが完了したらドメイン コントローラーを再起動します。

   ```powershell
   New-Item -Type Directory -Path '\\SEA-DC1.contoso.com\C$\Temp' -Force
   Copy-Item -Path "$env:USERPROFILE\Downloads\AzureADPasswordProtectionDCAgentSetup.msi" -Destination '\\SEA-DC1.contoso.com\C$\Temp\'
   Invoke-Command -ComputerName SEA-DC1.contoso.com -ScriptBlock { Start-Process msiexec.exe -ArgumentList '/i C:\Temp\AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart /log C:\Temp\AzureADPPInstall.log' -Wait }
   Restart-Computer -ComputerName SEA-DC1.contoso.com -Force
   ```
1. 次のコマンドを実行して、インストールによって Azure AD パスワード保護を実装するために必要なサービスが作成されたことを確認します。

   ```powershell
   Get-Service -Computer SEA-SVR1 -Name AzureADPasswordProtectionProxy | fl
   Get-Service -Computer SEA-DC1 -Name AzureADPasswordProtectionDCAgent | fl
   ```

   > **注**: 各サービスの状態が **[実行中]** であることを確認します。

1. **SEA-SVR1** への PowerShell リモート処理セッションを確立するには、次のコマンドを実行します。

   ```powershell
   Enter-PSSession -ComputerName SEA-SVR1
   ```

1. PowerShell リモート処理セッション内で次のコマンドを入力し、プロキシ サービスを Active Directory に登録します (プレースホルダー `<Azure_AD_Global_Admin>` を演習 1 で作成した Azure AD グローバル管理者ユーザー アカウントの完全修飾ユーザー プリンシパル名に置き換えます)。

   ```powershell
   Register-AzureADPasswordProtectionProxy -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

1. プロンプトに従い、演習 1 で作成 Azure AD グローバル管理者ユーザー アカウントを使用して認証を行います。 
1. PowerShell リモート処理セッションを終了します。
1. **Windows PowerShell** コンソールで、次のコマンドを入力してから、Enter キーを押して、**SEA-DC1** への PowerShell リモート処理セッションを開始します。

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1
   ```

1. PowerShell リモート処理セッション内で次のコマンドを入力し、プロキシ サービスを Active Directory に登録します (プレースホルダー `<Azure_AD_Global_Admin>` を演習 1 で作成した Azure AD グローバル管理者ユーザー アカウントの完全修飾ユーザー プリンシパル名に置き換えます)。

   ```powershell
   Register-AzureADPasswordProtectionForest -AccountUpn <Azure_AD_Global_Admin> -AuthenticateUsingDeviceCode
   ```

1. プロンプトに従い、演習 1 で作成 Azure AD グローバル管理者ユーザー アカウントを使用して認証を行います。 
1. PowerShell リモート処理セッションを終了します。

#### <a name="task-6-enable-password-protection-in-azure"></a>タスク 6: Azure でパスワード保護を有効にする

1. **SEA-ADM1** で、Azure portal が表示されている Microsoft Edge ウィンドウに切り替え、**[Azure Active Directory]** ページに戻り、**[セキュリティ]** ページを閲覧します。
1. **[セキュリティ]** ページで、**[認証方法]** を選択します。
1. **[認証方法]** ページで、**[パスワード保護]** を選択します。
1. **[パスワード保護]** ページで、**[カスタム リストの適用]** を有効にします。
1. **[カスタム禁止パスワード リスト]** テキスト ボックスに、次の単語を (1 行に 1 語) 入力します。

   - **Contoso**
   - **ロンドン**

   > **注**: 禁止パスワードの一覧は、ご自分の組織に関連する単語である必要があります。

1. **[Windows Server Active Directory でパスワード保護を有効にする]** の設定が有効になっていることを確認します。 
1. **[モード]** が **[監査]** に設定されていることを確認し、変更を保存します。

## <a name="exercise-6-cleaning-up"></a>演習 6: クリーンアップ

### <a name="scenario"></a>シナリオ

オンプレミスの Active Directory から Azure への同期を無効にします。 これには、Azure AD Connect の削除と Azure との同期の無効化が含まれます。

この演習の主なタスクは次のとおりです。

1. Azure AD Connect をアンインストールする。
1. Azure でディレクトリ同期を無効にする。

#### <a name="task-1-uninstall-azure-ad-connect"></a>タスク 1 Azure AD Connect のアンインストール

1. **SEA-ADM1** で、 **[コントロール パネル]** を開きます。
1. **[プログラムのアンインストールまたは変更]** 機能を使用して、**[Microsoft Azure AD Connect]** をアンインストールします。

#### <a name="task-2-disable-directory-synchronization-in-azure"></a>タスク 2: Azure でディレクトリ同期を無効にする

1. **SEA-ADM1** で、**Windows PowerShell** ウィンドウに切り替えます。
1. **Windows PowerShell** コンソールで、次のコマンドを実行し、Azure AD 用の Microsoft Online モジュールをインストールします。

   ```powershell
   Install-Module -Name MSOnline
   ```
1. 次のコマンドを実行して、資格情報を Azure に提供します。

   ```powershell
   $msolcred=Get-Credential
   ```
1. ダイアログが表示されたら、**[Windows PowerShell 資格情報要求]** ダイアログ ボックスで、演習 1 で作成したユーザー アカウントの資格情報を入力します。
1. 次のコマンドを実行して Azure に接続します。

   ```powershell
   Connect-MsolService -Credential $msolcred
   ```
1. 次のコマンドを実行して、Azure でディレクトリ同期を無効にします。

   ```powershell
   Set-MsolDirSyncEnabled -EnableDirSync $false
   ```

### <a name="prepare-for-the-next-module"></a>次のモジュールの準備

次のモジュールの準備が完了したら、ラボを終了します。
