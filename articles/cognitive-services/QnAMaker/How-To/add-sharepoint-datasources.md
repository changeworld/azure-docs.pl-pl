---
title: Pliki programu SharePoint — program QnA Maker
description: Dodaj zabezpieczone źródła danych programu SharePoint do bazy wiedzy, aby wzbogacić bazę wiedzy o pytania i odpowiedzi, które mogą być zabezpieczone za pomocą usługi Active Directory.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294874"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Dodawanie zabezpieczonego źródła danych programu SharePoint do bazy wiedzy

Dodaj zabezpieczone źródła danych programu SharePoint oparte na chmurze do bazy wiedzy, aby wzbogacić bazę wiedzy o pytania i odpowiedzi, które mogą być zabezpieczone za pomocą usługi Active Directory.

Po dodaniu zabezpieczonego dokumentu programu SharePoint do bazy wiedzy jako menedżera programu QnA Maker należy zażądać uprawnień usługi Active Directory dla programu QnA Maker. Po nadaniu menedżera usługi Active Directory programowi QnA Maker dostępu do programu SharePoint nie musi ono być ponownie udzielone. Każdy kolejny dokument oprócz bazy wiedzy nie będzie wymagał autoryzacji, jeśli znajduje się w tym samym zasobie programu SharePoint.

Jeśli menedżer bazy wiedzy QnA Maker nie jest menedżerem usługi Active Directory, aby zakończyć ten proces, należy skontaktować się z menedżerem usługi Active Directory.

## <a name="prerequisites"></a>Wymagania wstępne

* Program SharePoint oparty na chmurze — program QnA Maker używa programu Microsoft Graph do uzyskania uprawnień. Jeśli program SharePoint jest lokalny, nie będzie można wyodrębnić z programu SharePoint, ponieważ program Microsoft Graph nie będzie mógł określić uprawnień.
* Format adresu URL — program QnA Maker obsługuje tylko adresy URL programu SharePoint, które są generowane do udostępniania i są w formacie`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Dodawanie obsługiwanych typów plików do bazy wiedzy

Do bazy wiedzy można dodać wszystkie [typy plików](../Concepts/content-types.md) obsługiwane przez program QnA Maker z witryny programu SharePoint. Może być wymagane przyznanie [uprawnień, jeśli zasób](#permissions) pliku jest zabezpieczony.

1. Z biblioteki z witryną programu SharePoint wybierz menu `...`wielokropka pliku , .
1. Skopiuj adres URL pliku.

   ![Pobierz adres URL pliku programu SharePoint, wybierając menu wielokropek pliku, a następnie skopiując adres URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. W portalu QnA Maker na stronie **Ustawienia** [dodaj adres URL](manage-knowledge-bases.md#edit-knowledge-base) do bazy wiedzy.

### <a name="images-with-sharepoint-files"></a>Obrazy z plikami programu SharePoint

Jeśli pliki zawierają obrazy, nie są wyodrębniane. Obraz można dodać z portalu QnA Maker po wyodrębnianiu pliku do par QnA.

Dodaj obraz z następującą składnią znaczników:

```markdown
![Explanation or description of image](URL of public image)
```

Tekst w nawiasach kwadratowych, `[]`, wyjaśnia obraz. Adres URL w nawiasach `()`, jest bezpośrednim linkiem do obrazu.

Podczas testowania pary QnA w interaktywnym panelu testowym w portalu QnA Maker wyświetlany jest obraz zamiast tekstu znaczników. Sprawdza, czy obraz może być publicznie pobierany z aplikacji klienckiej.

## <a name="permissions"></a>Uprawnienia

Przyznawanie uprawnień ma miejsce, gdy zabezpieczony plik z serwera programu SharePoint zostanie dodany do bazy wiedzy. W zależności od konfiguracji programu SharePoint i uprawnień osoby dodającej plik może to wymagać:

* żadnych dodatkowych kroków - osoba dodająca plik ma wszystkie wymagane uprawnienia.
* zarówno przez [menedżera bazy wiedzy,](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) jak i [menedżera usługi Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Zobacz poniższe kroki.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Menedżer bazy wiedzy: dodawanie źródła danych programu SharePoint w portalu programu QnA Maker

Gdy **menedżer programu QnA Maker** doda zabezpieczony dokument programu SharePoint do bazy wiedzy, menedżer bazy wiedzy inicjuje żądanie uprawnień, które menedżer usługi Active Directory musi wykonać.

Żądanie rozpoczyna się od okna podręcznego w celu uwierzytelnienia na koncie usługi Active Directory.

![Uwierzytelnij konto użytkownika](../media/add-sharepoint-datasources/authenticate-user-account.png)

Gdy menedżer programu QnA Maker wybierze konto, administrator usługi Azure Active Directory otrzyma powiadomienie, że musi zezwolić aplikacji QnA Maker (nie menedżerowi QnA Maker) na dostęp do zasobu programu SharePoint. Menedżer usługi Azure Active Directory będzie musiał to zrobić dla każdego zasobu programu SharePoint, ale nie dla każdego dokumentu w tym zasobie.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Menedżer usługi Active Directory: udzielanie dostępu do odczytu plików do programu QnA Maker

Menedżer usługi Active Directory (a nie menedżer programu QnA Maker) musi udzielić dostępu do programu QnA Maker w celu uzyskania dostępu do zasobu programu SharePoint, wybierając [to łącze,](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) aby autoryzować aplikację QnA Maker Portal SharePoint enterprise do uzyskania uprawnień do odczytu pliku.

![Menedżer usługi Azure Active Directory udziela uprawnień interaktywnie](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Udzielanie dostępu z centrum administracyjnego usługi Azure Active Directory

1. Menedżer usługi Active Directory loguje się do portalu Azure i otwiera **[aplikacje enterprise](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. `QnAMakerPortalSharePoint` Wyszukaj wybierz aplikację QnA Maker.

    [![Wyszukaj listę aplikacji QnAMakerPortalSharePoint na liście aplikacji dla przedsiębiorstw](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. W obszarze **Zabezpieczenia**przejdź do **pozycji Uprawnienia**. Wybierz **pozycję Przyznaj zgodę administratora dla organizacji**.

    [![Wybieranie uwierzytelnionego użytkownika dla administratora usługi Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Wybierz konto logowania z uprawnieniami do udzielania uprawnień do usługi Active Directory.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współpraca nad bazą wiedzy](collaborate-knowledge-base.md)
