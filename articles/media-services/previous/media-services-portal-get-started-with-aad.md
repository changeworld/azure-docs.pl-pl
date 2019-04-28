---
title: Rozpoczynanie pracy z usługą uwierzytelniania usługi Azure AD przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostęp do uwierzytelniania usługi Azure Active Directory (Azure AD) korzystanie z interfejsu API usługi Azure Media Services za pomocą witryny Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 37c5a97d5f7a872e916f223f2cdfc98bcdec3cad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61130753"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Rozpoczynanie pracy z usługą uwierzytelniania usługi Azure AD przy użyciu witryny Azure portal

Dowiedz się, jak dostęp do uwierzytelniania usługi Azure Active Directory (Azure AD) do uzyskania dostępu do interfejsu API usługi Azure Media Services za pomocą witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, skorzystaj z [bezpłatna wersja próbna platformy](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure portal](media-services-portal-create-account.md).
- Upewnij się, przejrzeniu [uzyskiwania dostępu do usługi Azure Media Services interfejsu API za pomocą omówienie uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Gdy używasz uwierzytelniania usługi Azure AD za pomocą usługi Azure Media Services, masz dwie opcje uwierzytelniania:

- **Uwierzytelnianie użytkownika**. Uwierzytelnianie osobę, która jest za pomocą aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja najpierw powinien zostać wyświetlony monit o podanie poświadczeń użytkownika. Przykładem jest aplikacja do konsoli zarządzania używany przez użytkowników autoryzowanych do monitorowania zadań kodowania lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie jednostki usługi**. Uwierzytelnianie usługi. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które działają usługi demona, usługi warstwy środkowej lub zaplanowane zadanie: aplikacje sieci web, aplikacji funkcji, logic apps, interfejsów API lub mikrousług.

> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania usługi Azure Access Control. Jednak kontroli dostępu autoryzacji staną się przestarzałe 1 czerwca 2018 r. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="select-the-authentication-method"></a>Wybierz metodę uwierzytelniania

1. W [witryny Azure portal](https://portal.azure.com/), wybierz konto usługi Media Services.
2. Wybierz sposób nawiązywania połączeń z interfejsem API usług Media.

    ![Wybierz stronę metoda połączenia](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Uwierzytelnianie użytkowników

Aby połączyć się z interfejsem API Media Services przy użyciu opcji Uwierzytelnianie użytkownika, aplikacja kliencka musi żądać tokenu usługi Azure AD, która ma następujące parametry:  

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services
* Identyfikator klienta aplikacji Media Services (native) 
* Identyfikator URI przekierowania aplikacji Media Services (native) 
* Identyfikator URI dla usługi Media Services REST zasobu

Możesz też uzyskać wartości tych parametrów **interfejsu API usługi Media Services przy użyciu uwierzytelniania użytkownika** strony. 

![Łączenie ze stroną uwierzytelniania użytkownika](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Jeśli łączysz się z interfejsem API usług Media za pomocą zestawu Media Services w programie Microsoft .NET SDK, wymagane wartości są dostępne jako część zestawu SDK. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie usługi Azure AD dostęp do interfejsu API usługi multimediów Azure przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md).

Jeśli nie używasz zestawu SDK klienta platformy .NET usług Media Services, należy ręcznie utworzyć żądanie tokenu usługi Azure AD przy użyciu parametrów omówionych powyżej. Aby uzyskać więcej informacji, zobacz [jak uzyskiwanie tokenu usługi Azure AD za pomocą usługi Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby połączyć się z interfejsem API Media Services przy użyciu opcji jednostki usługi, aplikacji warstwy środkowej (interfejs API sieci web lub aplikacji sieci web) musi żądać tokenu usługi Azure AD, która ma następujące parametry:  

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services 
* Identyfikator URI dla usługi Media Services REST zasobu
* Wartości aplikacji w usłudze Azure AD: **identyfikator klienta** i **klucz tajny klienta**

Możesz też uzyskać wartości tych parametrów **nawiązywanie połączenia z interfejsem API usługi Media Services przy użyciu jednostki usługi** strony. Użyj tej strony, aby utworzyć nową aplikację usługi Azure AD, lub wybrać istniejącą grupę. Po wybraniu aplikacji usługi Azure AD, możesz uzyskać identyfikator klienta (identyfikator aplikacji) i generowanie wartości secret (klucz) klienta. 

![Łączenie ze stroną główną usługi](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Gdy **nazwy głównej usługi** zostanie otwarty blok, wybrano pierwszej aplikacji usługi Azure AD, która spełnia następujące kryteria:

- Jest zarejestrowaną aplikację usługi Azure AD.
- Ma uprawnienia współautora lub Owner Role-Based kontroli dostępu na koncie.

Po utworzeniu, lub wybierz aplikację usługi Azure AD, można tworzyć i skopiować klucza tajnego klienta (klucz) i identyfikator klienta (identyfikator aplikacji). Klucz tajny klienta i identyfikator klienta są wymagane, można uzyskać dostęp do tokenu w tym scenariuszu.

Jeśli nie masz uprawnień do tworzenia aplikacji w usłudze Azure AD w domenie, kontrolki aplikacji usługi Azure AD w bloku nie są wyświetlane i zostanie wyświetlony komunikat ostrzegawczy.

Jeśli łączysz się z interfejsem API usług Media za pomocą zestawu SDK .NET usługi Media Services, zobacz [uwierzytelnianie usługi Azure AD dostęp do interfejsu API usługi multimediów Azure przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md).

Jeśli nie używasz zestawu SDK klienta platformy .NET usług Media Services, należy ręcznie utworzyć żądania tokenu usługi Azure AD przy użyciu parametrów omówionych powyżej. Aby uzyskać więcej informacji, zobacz [jak uzyskiwanie tokenu usługi Azure AD za pomocą usługi Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Uzyskaj identyfikator klienta i klucz tajny klienta

Po wybraniu istniejącej aplikacji usługi Azure AD, lub wybierz opcję, aby utworzyć nową, wyświetlane są następujące przyciski:

![Zarządzanie uprawnieniami oraz zarządzanie aplikacji służącymi](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Aby otworzyć blok aplikacji usługi Azure AD, kliknij przycisk **Zarządzanie aplikacją**. Na **Zarządzanie aplikacją** bloku, możesz uzyskać identyfikator klienta aplikacji (identyfikator aplikacji). Aby wygenerować klucz tajny klienta (klucz), wybierz pozycję **klucze**.

![Zarządzanie opcja klucze bloku aplikacji](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Zarządzanie uprawnieniami i aplikacji

Po wybraniu aplikacji usługi Azure AD, możesz zarządzać aplikację i uprawnienia. Aby skonfigurować aplikację usługi Azure AD, aby uzyskiwać dostęp do innych aplikacji, kliknij przycisk **zarządzać uprawnieniami**. Dla zadań zarządzania, takich jak Zmienianie kluczy i adresów URL odpowiedzi lub edytować manifest aplikacji, kliknij przycisk **Zarządzanie aplikacją**.

### <a name="edit-the-apps-settings-or-manifest"></a>Edytowanie ustawień aplikacji lub manifest

Aby edytować ustawienia lub manifest aplikacji, kliknij przycisk **Zarządzanie aplikacją**.

![Zarządzanie stroną aplikacji](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą [przekazywania plików na koncie](media-services-portal-upload-files.md).
