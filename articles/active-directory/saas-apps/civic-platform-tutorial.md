---
title: チュートリアル:Azure Active Directory と Civic Platform の統合 | Microsoft Docs
description: Azure Active Directory と Civic Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.openlocfilehash: 26109d4b8875dd0b442521513dbd219dc0de06e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455971"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>チュートリアル:Civic Platform と Azure Active Directory の統合

このチュートリアルでは、Civic Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Civic Platform を統合すると、次のことができます。

* Civic Platform にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Civic Platform に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Civic Platform でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Civic Platform では、**SP** Initiated SSO がサポートされます





## <a name="adding-civic-platform-from-the-gallery"></a>ギャラリーからの Civic Platform の追加

Azure AD への Civic Platform の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Civic Platform を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Civic Platform**」と入力します。
1. 結果ウィンドウで **[Civic Platform]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Civic Platform に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Civic Platform の関連ユーザーの間で、リンク関係を確立する必要があります。

Civic Platform で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Civic Platform SSO の構成](#configure-civic-platform-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Civic Platform のテスト ユーザーの作成](#create-civic-platform-test-user)** - Civic Platform で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Civic Platform** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.accela.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、`civicplatform.accela.com` という URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 この値は実際のサインオン URL で更新します。 この値を取得するには、[Civic Platform クライアント サポート チーム](mailto:skale@accela.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![スクリーンショットは、アプリのフェデレーション メタデータ URL をコピーできる [SAML 署名証明書] ページを示しています。](common/copy-metadataurl.png)

1. **Azure Active Directory** >  Azure AD の **[アプリの登録]** の順に移動し、アプリケーションを選択します。

1. **ディレクトリ (テナント) ID** をコピーし、メモ帳に保存します。

    ![ディレクトリ (テナント ID) をコピーし、自分のアプリ コードに保存する](media/civic-platform-tutorial/directoryid.png)

1. **アプリケーション ID** をコピーし、メモ帳に保存します。

   ![アプリケーション (クライアント) ID をコピーする](media/civic-platform-tutorial/applicationid.png)

1. **Azure Active Directory** >  Azure AD の **[アプリの登録]** の順に移動し、アプリケーションを選択します。 **[証明書とシークレット]** を選択します。

1. **[クライアント シークレット] -> [新しいクライアント シークレット]** を選択します。

1. シークレットの説明と期間を指定します。 完了したら、 **[追加]** をクリックします。

   > [!NOTE]
   > クライアント シークレットを保存すると、クライアント シークレットの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。

   ![後からこれを取得することはできないので、このシークレット値をコピーする](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Civic Platform の SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、Atlassian Cloud 企業サイトに管理者としてサインインします。

1. **[Standard Choices]\(標準選択項目\)** をクリックします。

    ![スクリーンショットは、[Administrator Tools]\(管理ツール\) で [Standard Choices]\(標準選択項目\) が選択されている Atlassian Cloud サイトを示しています。](media/civic-platform-tutorial/standard-choices.png)

1. 標準選択項目 **ssoconfig** を作成します。

1. **ssoconfig** を検索して送信します。

    ![スクリーンショットは、ssoconfig という名前が入力された [Standard Choices - Search]\(標準選択項目 - 検索\) を示しています。](media/civic-platform-tutorial/sso-config.png)

1. 赤い点をクリックして、[SSOCONFIG] を展開します。

    ![スクリーンショットは、S S O CONFIG が利用できる [Standard Choices - Browse]\(標準選択項目 - 参照\) を示しています。](media/civic-platform-tutorial/sso-config01.png)

1. 次の手順に従って、SSO 関連の構成情報を指定します。

    ![スクリーンショットは、S S O CONFIG の [Standard Choices Item - Edit]\(標準選択項目 - 編集\) を示しています。](media/civic-platform-tutorial/sso-config02.png)

    1. **[applicationid]** フィールドに、Azure portal からコピーした **アプリケーション ID** の値を入力します。

    1. **[clientSecret]** フィールドに、Azure portal からコピーした **シークレット** の値を入力します。

    1. **[directoryId]** フィールドに、Azure portal からコピーした **ディレクトリ (テナント) ID** の値を入力します。

    1. [idpName] を入力します。 例: `Azure`。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Civic Platform へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Civic Platform]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-civic-platform-test-user"></a>Civic Platform のテスト ユーザーの作成

このセクションでは、Civic Platform で B.Simon というユーザーを作成します。 Civic Platform サポート チームと連携し、[Civic Platform クライアント サポート チーム](mailto:skale@accela.com)にユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Civic Platform] タイルをクリックすると、SSO を設定した Civic Platform に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)