---
title: Azure AD でのアプリケーション プロビジョニングの既知の問題
description: Azure AD で自動アプリケーション プロビジョニングを使用する場合の既知の問題について説明します。
author: kenwith
ms.author: kenwith
manager: daveba
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: 9eba671f6c824c8c88388f2b9d61512dfb1d122f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256645"
---
# <a name="known-issues-application-provisioning"></a>既知の問題:アプリケーションのプロビジョニング
アプリのプロビジョニングを使用するときに注意する必要がある既知の問題。 UserVoice のアプリケーション プロビジョニング サービスに関するフィードバックを提供することができます。[Azure AD のアプリケーションのプロビジョニングの UserVoice](https://aka.ms/appprovisioningfeaturerequest) ページを参照してください。 Microsoft では、サービスを改善するために UserVoice を注意深く確認しています。 

> [!NOTE]
> これは既知の問題の包括的な一覧ではありません。 一覧にない問題をご存じの場合は、ページの下部でフィードバックを提供してください。

## <a name="authorization"></a>承認 

**接続テストが成功した後に保存できない**

接続は正常にテストできても保存できない場合は、資格情報に許容されているストレージの上限を超えています。 詳細については、[管理者の資格情報の保存に関する問題](./user-provisioning.md)のページを参照してください。

**保存できない**

保存するには、テナントの URL、シークレット トークン、通知メールを入力する必要があります。 1 つだけを指定することはできません。 

**プロビジョニング モードを手動に変更することができない**

プロビジョニングを初めて構成すると、プロビジョニング モードが手動から自動に切り替わることがわかります。 手動に戻すことはできません。 ただし、UI を使用してプロビジョニングをオフにすることはできます。 UI でプロビジョニングをオフにすると、ドロップダウンを手動に設定する場合と同様に動作します。  


## <a name="attribute-mappings"></a>属性マッピング 

**属性 SamAccountName または userType をソース属性として使用できない**

属性 SamAccountName および userType は、既定ではソース属性として使用できません。 スキーマを拡張して属性を追加してください。 スキーマを拡張すると、使用できるソース属性の一覧に属性を追加することができます。 詳細については、[ソース属性の欠落い](user-provisioning-sync-attributes-for-mapping.md)に関するページを参照してください。 

**スキーマ拡張のソース属性ドロップダウンがない**

UI のソース属性ドロップダウンにスキーマの拡張機能が表示されない場合があります。 属性マッピングの詳細設定に移動し、手動で属性を追加します。 詳細については、[属性マッピングのカスタマイズ](customize-application-attributes.md)に関するページを参照してください。

**null 属性をプロビジョニングできない**

現在、Azure AD によって null 属性をプロビジョニングすることはできません。 ユーザー オブジェクトの属性が null の場合は、スキップされます。 

**属性マッピング式の最大文字数**

属性マッピング式には、最大 10,000 文字を使用できます。 

**サポートされていないスコープ フィルター**

ディレクトリ拡張機能、appRoleAssignments、userType、および accountExpires は、スコープ フィルターとしてはサポートされていません。


## <a name="service-issues"></a>サービスに関する問題 

**サポートされていないシナリオ**

- パスワードのプロビジョニングはサポートされていません。 
- 入れ子になったグループのプロビジョニングはサポートされていません。 
- テナントのサイズにより、B2C テナントへのプロビジョニングはサポートされていません。
- すべてのクラウドですべてのプロビジョニング アプリが使用できるわけではありません。 たとえば、Atlassian は、政府機関向けクラウドではまだ利用できません。 アプリ開発者と連携して、アプリをすべてのクラウドにオンボードしています。

**OIDC ベースのアプリケーションで自動プロビジョニングを使用できない**

アプリの登録を作成した場合、エンタープライズ アプリ内の対応するサービス プリンシパルが、自動ユーザー プロビジョニングで有効になりません。 ギャラリーにアプリを追加するように要求するか (複数の組織での使用を想定している場合)、プロビジョニング用にギャラリー以外の 2 つ目のアプリを作成する必要があります。 

**プロビジョニングの間隔が固定されている**

プロビジョニング サイクル間の[時間](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)は現在構成できません。 

**変更がターゲット アプリから Azure AD に反映されない**

アプリのプロビジョニング サービスでは、外部アプリで行われた変更が認識されません。 そのため、ロールバックするアクションは行われません。 アプリのプロビジョニング サービスは、Azure AD で行われた変更に依存しています。 

**"すべて同期" から "割り当てられた同期" への切り替えが機能しない**

"すべて同期" から "割り当てられた同期" にスコープを変更したら、変更を有効にするために必ず再起動も実行してください。 再起動は UI から実行できます。

**プロビジョニング サイクルが完了するまで続行される**

`enabled = off` のプロビジョニングを設定するか、停止すると、現在のプロビジョニング サイクルが完了するまで実行は続行されます。 プロビジョニングを再度有効にするまで、サービスによって今後のサイクルの実行が停止されます。

**グループのメンバーがプロビジョニングされない**

グループがスコープ内にあり、メンバーがスコープ外にある場合、グループはプロビジョニングされます。 スコープ外のユーザーはプロビジョニングされません。 メンバーがスコープ内に戻っても、サービスではすぐに変更が検出されません。 プロビジョニングを再開すると、問題は解決します。 すべてのユーザーが適切にプロビジョニングされることを確保するため、定期的にサービスを再起動することをお勧めします。  

**マネージャーがプロビジョニングされない**

ユーザーとそのマネージャーが両方ともプロビジョニング対象となっている場合は、ユーザーがプロビジョニングされた後でマネージャーが更新されます。 ただし初日に、ユーザーはプロビジョニング対象でもマネージャーが対象外となっている場合、マネージャーを参照せずにユーザーがプロビジョニングされます。 その後マネージャーがプロビジョニング対象となっても、プロビジョニングを再開し、サービスによってすべてのユーザーがもう一度評価されるまで、マネージャーの参照は更新されません。 

## <a name="next-steps"></a>次のステップ
- [プロビジョニングのしくみ](how-provisioning-works.md)
