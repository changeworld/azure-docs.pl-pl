---
title: Wprowadzenie do uwierzytelniania usługi Azure AD przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać portalu Azure do uwierzytelniania usługi Azure Active Directory (Azure AD) do korzystania z usługi Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 6267dd8dca4c932d4a4d96b34a8eaa26f6a59c20
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790328"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Wprowadzenie do uwierzytelniania usługi Azure AD przy użyciu portalu Azure

Dowiedz się, jak używać portalu Azure do uwierzytelniania usługi Azure Active Directory (Azure AD) można uzyskać dostępu do interfejsu API Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, Rozpocznij od [Azure bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [utworzenie konta usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).
- Upewnij się, że należy przejrzeć [podczas uzyskiwania dostępu do usługi Azure Media usług interfejsu API za pomocą omówienie uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Korzystając z uwierzytelniania usługi Azure AD z usługi Azure Media Services, masz dwie opcje uwierzytelniania:

- **Uwierzytelnianie użytkownika**. Uwierzytelnianie osoby, która korzysta z aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja powinna pierwszy monit o podanie poświadczeń. Przykładem jest aplikacja konsoli zarządzania używane przez autoryzowanych użytkowników do monitorowania kodowania zadań lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie głównej usługi**. Uwierzytelniania usługi. Aplikacje, które zazwyczaj używają tej metody uwierzytelniania są aplikacji uruchamianych demon usługi, usługi warstwy środkowej lub zaplanowane zadania: aplikacje sieci web funkcji aplikacji, aplikacji logiki, interfejsów API albo mikrousługi.

> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania usługi kontroli dostępu platformy Azure. Jednak na 1 czerwca 2018 zostaną wycofane autoryzacji kontroli dostępu. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="select-the-authentication-method"></a>Wybierz metodę uwierzytelniania

1. W [portalu Azure](https://portal.azure.com/), wybierz konto usługi Media Services.
2. Wybierz sposób nawiązywania połączenia z programem Media Services API.

    ![Wybierz połączenie stronę — metoda](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Uwierzytelnianie użytkowników

Aby połączyć się z interfejsu API usług Media przy użyciu opcji uwierzytelniania użytkownika, aplikacja kliencka musi żądania tokenu usługi Azure AD, który ma następujące parametry:  

* Punktu końcowego dzierżawcy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services
* Identyfikator klienta aplikacji usługi Media Services (native) 
* Identyfikator URI przekierowania aplikacji usługi Media Services (native) 
* Identyfikator URI dla usługi REST Media Services

Wartości tych parametrów można uzyskać **Media Services API z uwierzytelnieniem użytkownika** strony. 

![Uzyskuj dostęp do strony uwierzytelniania użytkownika](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Jeśli łączysz się interfejsu API usług Media Services przy użyciu zestawu .NET SDK Media Services Microsoft wymagane wartości są dostępne jako część zestawu SDK. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure z platformą .NET](media-services-dotnet-get-started-with-aad.md).

Jeśli nie używasz klienta Media Services na platformie .NET SDK, należy ręcznie utworzyć żądania tokenu usługi Azure AD przy użyciu parametrów wcześniejszym opisem. Aby uzyskać więcej informacji, zobacz [Pobierz token programu Azure AD przy użyciu usługi Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby połączyć się interfejsu API usług Media Services przy użyciu opcji głównej usługi, aplikacji warstwy środkowej (interfejs API sieci web lub aplikacji sieci web) musi żądania tokenu usługi Azure AD, który ma następujące parametry:  

* Punktu końcowego dzierżawcy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services 
* Identyfikator URI dla usługi REST Media Services
* Wartości aplikacji w usłudze Azure AD: **identyfikator klienta** i **klucz tajny klienta**

Wartości tych parametrów można uzyskać **Połącz z interfejsu API usług Media z nazwy głównej usługi** strony. Użyj tej strony, aby utworzyć nową aplikację usługi Azure AD lub wybierz istniejący. Po wybraniu aplikacji usługi Azure AD, można uzyskać Identyfikatora klienta (identyfikator aplikacji) i generowania wartości (klucz) klucz tajny klienta. 

![Uzyskuj dostęp do strony głównej usługi](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Gdy **nazwy głównej usługi** zostanie otwarty blok, wybraniu pierwszej aplikacji usługi Azure AD, która spełnia następujące kryteria:

- Jest zarejestrowaną aplikację usługi Azure AD.
- Konto ma uprawnienia współautora lub Owner Role-Based kontroli dostępu.

Po utworzeniu, lub wybierz aplikację usługi Azure AD, można tworzyć i skopiuj klucz tajny klienta (klucz) i identyfikator klienta (identyfikator aplikacji). Klucz tajny klienta i identyfikator klienta są wymagane uzyskanie dostępu do tokenu w tym scenariuszu.

Jeśli nie masz uprawnień do tworzenia aplikacji Azure AD w domenie, kontrolek aplikacji usługi Azure AD w bloku nie są wyświetlane, a następnie zostanie wyświetlony komunikat ostrzegawczy.

Jeśli łączysz się do interfejsu API usług Media Services przy użyciu zestawu .NET SDK usługi multimediów, zobacz [uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure z platformą .NET](media-services-dotnet-get-started-with-aad.md).

Jeśli nie używasz klienta Media Services na platformie .NET SDK, należy ręcznie utworzyć żądania tokenu usługi Azure AD przy użyciu parametrów wcześniejszym opisem. Aby uzyskać więcej informacji, zobacz [Pobierz token programu Azure AD przy użyciu usługi Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Pobierz identyfikator klienta i klucz tajny klienta

Po wybierz istniejącą aplikację usługi Azure AD, lub wybierz opcję, aby utworzyć nową, wyświetlane są następujące przyciski:

![Zarządzanie przyciski uprawnienia i zarządzanie aplikacji](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Aby otworzyć blok aplikacji usługi Azure AD, kliknij przycisk **Zarządzanie aplikacją**. Na **Zarządzanie aplikacją** bloku, możesz uzyskać identyfikator aplikacji klienta (identyfikator aplikacji). Aby wygenerować klucz tajny klienta (key), wybierz **klucze**.

![Zarządzanie aplikacji bloku klucze opcji](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Zarządzanie uprawnieniami i aplikacji

Po wybraniu aplikacji usługi Azure AD, można zarządzać aplikacji i uprawnienia. Aby skonfigurować aplikację usługi Azure AD na dostęp do innych aplikacji, kliknij przycisk **zarządzania uprawnieniami**. Dla zadań zarządzania, takich jak zmiana kluczy i adresy URL odpowiedzi lub edytować manifest aplikacji, kliknij przycisk **Zarządzanie aplikacją**.

### <a name="edit-the-apps-settings-or-manifest"></a>Edytowanie ustawień aplikacji lub manifest

Aby edytować ustawienia lub manifest aplikacji, kliknij przycisk **Zarządzanie aplikacją**.

![Zarządzanie strony aplikacji](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z [przekazywanie plików do konta](media-services-portal-upload-files.md).
