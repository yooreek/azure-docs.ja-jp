---
title: 'チュートリアル: Britive を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure AD から Britive へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225672"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>チュートリアル: Britive を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Britive と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用して、 [Britive](https://www.britive.com/) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Britive でユーザーを作成する
> * アクセスが不要になった場合に Britive のユーザーを削除する
> * Azure AD と Britive の間でのユーザー属性の同期を維持する
> * Britive にグループとグループ メンバーシップをプロビジョニングする
> * Britive への[シングル サインオン](britive-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [Britive](https://www.britive.com/) テナント。
* 管理者アクセス許可がある Britive のユーザー アカウント。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と Britive の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Britive を構成する

アプリケーションは、次の手順に従って手動で構成する必要があります。
1. 管理者特権で Britive アプリケーションにログインする
1. **[管理] -> [ユーザー管理] -> [ID プロバイダー]** をクリックします
1. **[ID プロバイダーの追加]** をクリックします。 名前と説明を入力します。 [ID プロバイダーの追加] ボタンをクリックします。

    ![ID プロバイダー](media/britive-provisioning-tutorial/identity.png)

1. 次のような構成ページが表示されます。

    ![[構成] ページ](media/britive-provisioning-tutorial/configuration.png)

1. **[SCIM]** タブをクリックします。SCIM プロバイダーを Generic から Azure に変更し、変更を保存します。 SCIM URL をコピーしてメモします。この値は、Azure portal の Britive アプリケーションの [プロビジョニング] タブにある **[テナント URL]** ボックスに入力されます。

    ![SCIM ページ](media/britive-provisioning-tutorial/scim.png)

1. **[トークンの作成]** をクリックします。 必要に応じてトークンの有効性を選択し、[トークンの作成] ボタンをクリックします。

    ![トークンの作成](media/britive-provisioning-tutorial/create-token.png)

1. 生成されたトークンをコピーしてメモします。 [OK] をクリックします。 ユーザーはトークンを再度確認することはできませんのでご注意ください。 [再作成] ボタンをクリックして、必要に応じて新しいトークンを生成します。この値は、Azure portal の getAbstract アプリケーションの [プロビジョニング] タブにある **[シークレット トークン]** と [テナント URL] ボックスに入力されます。

    ![トークンのコピー](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Britive を追加する

Azure AD アプリケーション ギャラリーから Britive を追加して、Britive へのプロビジョニングの管理を開始します。 SSO のために Britive を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Britive にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>手順 5. Britive への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Britive のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Azure AD で Britive の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Britive]** を選択します。

    ![アプリケーションの一覧の Britive のリンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、Britive の [テナントの URL] と [シークレット トークン] を入力します。 **[テスト接続]** をクリックして、Azure AD から Britive への接続を確保します。 接続できない場合は、その Britive アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Britive]\(Azure Active Directory ユーザーを Britive に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Britive に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Britive のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Britive API で確実にサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|
   |displayName|String|
   |title|String|
   |externalId|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |nickName|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |emails[type eq "work"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "home"].formatted|String|
   |addresses[type eq "home"].streetAddress|String|
   |addresses[type eq "home"].locality|String|
   |addresses[type eq "home"].region|String|
   |addresses[type eq "home"].postalCode|String|
   |addresses[type eq "home"].country|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "other"].streetAddress|String|
   |addresses[type eq "other"].locality|String|
   |addresses[type eq "other"].region|String|
   |addresses[type eq "other"].postalCode|String|
   |addresses[type eq "other"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|


1. **[マッピング]** セクションで、 **[Azure Active Directory グループを Britive に同期する]** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Britive に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Britive のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;
      |externalId|String|
      |members|リファレンス|

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

1. Britive に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Britive にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)