---
title: チュートリアル:Azure Active Directory と New Relic by Account の統合 | Microsoft Docs
description: Azure Active Directory と New Relic by Account の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a2c149bfdf79102779abf7544fed9fb78796a50e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649966"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と New Relic by Account の統合

このチュートリアルでは、New Relic by Account と Azure Active Directory (Azure AD) を統合する方法について説明します。 New Relic by Account を Azure AD と統合すると、次のことができます。

* New Relic by Account にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して自動的に New Relic by Account にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* New Relic by Account でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* New Relic by Account では、**SP** Initiated SSO がサポートされます

## <a name="add-new-relic-by-account-from-the-gallery"></a>ギャラリーからの New Relic by Account の追加

Azure AD への New Relic by Account の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に New Relic by Account を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**New Relic by Account**」と入力します。
1. 結果パネルから **[New Relic by Account]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>New Relic by Account に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、New Relic by Account に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと New Relic by Account の関連ユーザーとの間にリンク関係を確立する必要があります。

New Relic by Account に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[New Relic by Account SSO の構成](#configure-new-relic-by-account-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[New Relic by Account テスト ユーザーの作成](#create-new-relic-by-account-test-user)** - New Relic by Account で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **New Relic by Account** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)
   
1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize` - 必ず `acc_id` を New Relic by Account の自分のアカウント ID に置き換えてください。

    b. **[識別子 (エンティティ ID)]** ボックスに `rpm.newrelic.com` という URL を入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[New Relic by Account のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

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

このセクションでは、B.Simon に New Relic by Account へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[New Relic by Account]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-new-relic-by-account-sso"></a>New Relic by Account SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として **New Relic by Account** 企業サイトにサインオンします。

2. 上部のメニューで **[アカウント設定]** をクリックします。
   
    ![ウェルカム ページを示すスクリーンショット。[アカウント設定] が選択されています。](./media/new-relic-tutorial/settings.png "[Account Settings]")

3. **[セキュリティと認証]** タブをクリックし、 **[シングル サインオン]** タブをクリックします。
   
    ![シングル サインオン](./media/new-relic-tutorial/single-sign-on-tab.png "[Single Sign-On]")

4. SAML ダイアログ ページで、次の手順に従います。
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. **[ファイルの選択]** をクリックして、ダウンロードした Azure Active Directory 証明書をアップロードします。

    b. **[リモート ログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。
   
    c. **[Logout landing URL]\(ログアウト ランディング URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[変更を保存する]** をクリックします。

### <a name="create-new-relic-by-account-test-user"></a>New Relic by Account テスト ユーザーの作成

1. **New Relic by Account** 企業サイトに管理者としてサインインします。

2. 上部のメニューで **[アカウント設定]** をクリックします。
   
    ![ウェルカム ページで [アカウント設定] が選択されていることを示すスクリーンショット。](./media/new-relic-tutorial/account.png "[Account Settings]")

3. **[アカウント]** ペインの左側にある **[概要]** をクリックし、次に **[ユーザーの追加]** をクリックします。
   
    ![[概要] ウィンドウを示すスクリーンショット。ここでは、[ユーザーの追加] を選択できます。](./media/new-relic-tutorial/add.png "[Account Settings]")

4. **[アクティブ ユーザー]** ダイアログで、次の手順に従います。
   
    ![アクティブ ユーザー](./media/new-relic-tutorial/user.png "[アクティブ ユーザー]")
   
    a. **[電子メール]** テキストボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの電子メール アドレスを入力します。

    b. **[ロール]** として **[ユーザー]** を選びます。

    c. **[このユーザーを追加]** をクリックします。

> [!NOTE]
> 他の New Relic by Account ユーザー アカウント作成ツールや、New Relic by Account から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる New Relic by Account のサインオン URL にリダイレクトされます。 

* New Relic by Account のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [New Relic by Account] タイルをクリックすると、New Relic by Account のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

New Relic by Account を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。