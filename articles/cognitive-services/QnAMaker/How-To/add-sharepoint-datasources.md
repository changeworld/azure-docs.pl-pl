---
title: Pliki programu SharePoint — QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodawanie źródeł danych w usłudze bezpiecznego programu Sharepoint do bazy wiedzy wzbogacić wiedzy pytań i odpowiedzi, które mogą zostać zabezpieczony za pomocą usługi Active Directory.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/26/2019
ms.author: tulasim
ms.openlocfilehash: 97da0608781c286e685fd70dd7be37192dd3c9fd
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580414"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Dodawanie bezpiecznego źródła danych programu Sharepoint do bazy wiedzy

Dodawanie źródeł danych w usłudze bezpiecznego programu Sharepoint do bazy wiedzy wzbogacić wiedzy pytań i odpowiedzi, które mogą zostać zabezpieczony za pomocą usługi Active Directory. 

Po dodaniu zabezpieczonego dokumentów programu Sharepoint do bazy wiedzy, jako Menedżer ds. usługi QnA Maker, należy zażądać uprawnień usługi Active Directory dotyczące usługi QnA Maker. Po to uprawnienie zostanie podany przez Menedżera usługi Active Directory do usługi QnA Maker do uzyskiwania dostępu do programu Sharepoint, nie musi to być podane ponownie. Każdy dodatkem następny dokument w bazie wiedzy nie będzie konieczne autoryzacji, jeśli znajduje się w ten sam zasób programu Sharepoint. 

Jeśli Menedżer bazy wiedzy usługi QnA Maker nie jest Menedżer usłudze Active Directory, konieczne będzie komunikować się z Menedżerem usługi Active Directory, aby zakończyć ten proces.

## <a name="add-supported-file-types-to-knowledge-base"></a>Dodaj obsługiwane typy plików do bazy wiedzy

Można dodać wszystkie obsługiwane usługi QnA Maker [typy plików](../Concepts/data-sources-supported.md) z serwera programu Sharepoint do bazy wiedzy. Musisz udzielić [uprawnienia](#permissions) Jeśli zasób plik jest zabezpieczony.

1. Z serwera programu Sharepoint, wybierz menu wielokropka dla pliku, `...`.
1. Skopiuj adres URL pliku.

    ![Pobierz adres URL pliku programu Sharepoint, wybierając z menu wielokropka pliku następnie kopiując adres URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. W portalu narzędzia QnA Maker na **ustawienia** stronie [Dodaj adres URL](edit-knowledge-base.md#add-datasource) w bazie wiedzy. 

## <a name="permissions"></a>Uprawnienia

Udzielanie uprawnień odbywa się, gdy zabezpieczonych plików z serwera programu Sharepoint zostanie dodany do bazy wiedzy. W zależności od konfiguracji programu Sharepoint w górę oraz uprawnienia, które osoby, dodanie plików, może to wymagać:

* żadne dodatkowe kroki — osoby, musisz dodać plik ma niezbędne uprawnienia.
* kroki zarówno [Menedżera bazy wiedzy knowledge base](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) i [Menedżer usłudze Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Zobacz kroki wymienione poniżej. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Menedżer bazy wiedzy: Dodawanie źródła danych programu Sharepoint w portalu narzędzia QnA Maker

Gdy **Menedżera usługi QnA Maker** dodaje zabezpieczonych dokumentów programu Sharepoint do wiedzy, inicjuje Menedżera bazy wiedzy knowledge base wniosek o uprawnienia, które musi wykonać Menedżer usłudze Active Directory.

Żądanie zaczyna się okno podręczne do uwierzytelniania konta usługi Active Directory. 

![Uwierzytelnianie konta użytkownika](../media/add-sharepoint-datasources/authenticate-user-account.png)

Po Menedżera usługi QnA Maker wybierze konta, administrator usługi Active Directory otrzyma powiadomienie, co potrzeba, aby dostęp do usługi QnA Maker aplikacji (a nie Menedżera usługi QnA Maker) zasób programu Sharepoint. Menedżer usłudze Active Directory, należy to zrobić dla każdego zasobu programu Sharepoint, ale nie każdy dokument w tego zasobu. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Menedżer usłudze Active directory: udzielanie dostępu do odczytu pliku do usługi QnA Maker

Menedżer usłudze Active Directory (a nie Menedżera usługi QnA Maker) musi udzielić dostępu do usługi QnA Maker dostępu do zasobu programu Sharepoint, wybierając [ten link](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=files.read%20openid%20profile&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) Aby autoryzować aplikację przedsiębiorstwa usługi QnA Maker portalu Sharepoint mają odczytywanie pliku uprawnienia. 

![Usługa Azure Active Directory manager przyznaje uprawnienia do interaktywnego](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)

### Grant access from the Azure Active Directory admin center

1. The Active Directory manager signs in to the Azure portal and opens **[Enterprise applications](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**. 

1. Search for `QnAMakerPortalSharepoint` the select the QnA Maker app. 

    [![Search for QnAMakerPortalSharepoint in Enterprise apps list](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Under **Security**, go to **Permissions**. Select **Grant admin consent for Organization**. 

    [![Select authenticated user for Active Directory Admin](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Select a Sign-On account with permissions to grant permissions for the Active Directory. 

-->
  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

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

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) or [updating a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Współpracuj nad bazy wiedzy](collaborate-knowledge-base.md)