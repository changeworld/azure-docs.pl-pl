---
title: Wprowadzenie do uwierzytelniania w usłudze Azure AD przy użyciu Azure Portal | Microsoft Docs
description: Dowiedz się, w jaki sposób używać Azure Portal do uzyskiwania dostępu do uwierzytelniania Azure Active Directory (Azure AD) w celu korzystania z interfejsu API Azure Media Services.
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
ms.openlocfilehash: 4428187c985c1004c88f2ac20b0e5811803cce2a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162773"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Wprowadzenie do uwierzytelniania w usłudze Azure AD przy użyciu Azure Portal

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Dowiedz się, jak za pomocą Azure Portal uzyskać dostęp do uwierzytelniania Azure Active Directory (Azure AD) w celu uzyskania dostępu do interfejsu API Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, Zacznij od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Media Services przy użyciu Azure Portal](media-services-portal-create-account.md).
- Zapoznaj się z [omówieniem uzyskiwania dostępu do interfejsu API Azure Media Services przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

W przypadku korzystania z uwierzytelniania usługi Azure AD z usługą Azure Media Services dostępne są dwie opcje uwierzytelniania:

- **Uwierzytelnianie użytkownika**. Uwierzytelnij osobę, która korzysta z aplikacji, aby korzystać z zasobów Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 
- **Uwierzytelnianie nazwy głównej usługi**. Uwierzytelnianie usługi. Aplikacje, które często używają tej metody uwierzytelniania, to aplikacje, które uruchamiają usługi demonów, usługi warstwy środkowej lub zaplanowane zadania: aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejsy API lub mikrousługi.

> [!IMPORTANT]
> Obecnie Media Services obsługuje model uwierzytelniania usługi Azure Access Control. Jednak autoryzacja Access Control będzie przestarzała 1 czerwca 2018. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="select-the-authentication-method"></a>Wybierz metodę uwierzytelniania

1. W [Azure Portal](https://portal.azure.com/)wybierz swoje konto Media Services.
2. Wybierz sposób nawiązywania połączenia z interfejsem API Media Services.

    ![Strona wybierania metody połączenia](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Uwierzytelnianie użytkowników

Aby nawiązać połączenie z interfejsem API Media Services przy użyciu opcji uwierzytelniania użytkownika, aplikacja kliencka musi zażądać tokenu usługi Azure AD, który ma następujące parametry:  

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu Media Services
* Identyfikator klienta aplikacji Media Services (natywny) 
* Identyfikator URI przekierowania aplikacji Media Services (natywny) 
* Identyfikator URI zasobu dla Media Services REST

Wartości tych parametrów można uzyskać w **interfejsie API Media Services ze stroną uwierzytelnianie użytkowników** . 

![Strona z uwierzytelnianiem użytkowników](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

W przypadku nawiązywania połączenia z interfejsem API Media Services przy użyciu zestawu SDK programu Media Services Microsoft .NET wymagane wartości są dostępne w ramach zestawu SDK. Aby uzyskać więcej informacji, zobacz [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API Azure Media Services przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md).

Jeśli nie używasz zestawu SDK klienta Media Services .NET, musisz ręcznie utworzyć żądanie tokenu usługi Azure AD przy użyciu opisanych wcześniej parametrów. Aby uzyskać więcej informacji, zobacz [jak używać biblioteki uwierzytelniania usługi Azure AD w celu uzyskania tokenu usługi Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby nawiązać połączenie z interfejsem API Media Services przy użyciu opcji głównej usługi, aplikacja warstwy środkowej (Web API lub aplikacja sieci Web) musi zażądać tokenu usługi Azure AD, który ma następujące parametry:  

* Punkt końcowy dzierżawy usługi Azure AD
* Identyfikator URI zasobu Media Services 
* Identyfikator URI zasobu dla Media Services REST
* Wartości aplikacji usługi Azure AD: **Identyfikator klienta** i **klucz tajny klienta**

Wartości tych parametrów można uzyskać na stronie **łączenie się z interfejsem API Media Services przy użyciu jednostki usługi** . Ta strona służy do tworzenia nowej aplikacji usługi Azure AD lub do wybrania istniejącej. Po wybraniu aplikacji usługi Azure AD możesz uzyskać identyfikator klienta (Identyfikator aplikacji) i wygenerować wartości klucza tajnego klienta (klucz). 

![Połącz ze stroną główną usługi](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Gdy zostanie otwarty blok **głównej usługi** , zostanie wybrana pierwsza aplikacja usługi Azure AD, która spełnia następujące kryteria:

- Jest to zarejestrowana aplikacja usługi Azure AD.
- Ma uprawnienia współautora lub właściciela Access Control na tym koncie.

Po utworzeniu lub wybraniu aplikacji usługi Azure AD można utworzyć i skopiować klucz tajny klienta (klucz) oraz identyfikator klienta (Identyfikator aplikacji). W tym scenariuszu wymagany jest klucz tajny klienta i identyfikator klienta.

Jeśli nie masz uprawnień do tworzenia aplikacji usługi Azure AD w domenie, formanty aplikacji usługi Azure AD w bloku nie są wyświetlane i zostanie wyświetlony komunikat ostrzegawczy.

W przypadku nawiązywania połączenia z interfejsem API Media Services przy użyciu zestawu SDK Media Services .NET, zobacz [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu api Azure Media Services przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md).

Jeśli nie używasz zestawu SDK klienta Media Services .NET, musisz ręcznie utworzyć żądanie tokenu usługi Azure AD przy użyciu opisanych wcześniej parametrów. Aby uzyskać więcej informacji, zobacz [jak używać biblioteki uwierzytelniania usługi Azure AD w celu uzyskania tokenu usługi Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Pobierz identyfikator klienta i klucz tajny klienta

Po wybraniu istniejącej aplikacji usługi Azure AD lub wybraniu opcji tworzenia nowego elementu są wyświetlane następujące przyciski:

![Przycisk Zarządzaj uprawnieniami i przycisk Zarządzaj aplikacjami](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Aby otworzyć blok aplikacji usługi Azure AD, kliknij pozycję **Zarządzaj aplikacją**. W bloku **Zarządzanie aplikacją** można uzyskać identyfikator klienta (Identyfikator aplikacji) aplikacji. Aby wygenerować klucz tajny klienta (klucz), wybierz pozycję **klucze**.

![Opcja zarządzania kluczami bloku aplikacji](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Zarządzanie uprawnieniami i aplikacją

Po wybraniu aplikacji usługi Azure AD możesz zarządzać aplikacją i uprawnieniami. Aby skonfigurować aplikację usługi Azure AD w celu uzyskania dostępu do innych aplikacji, kliknij pozycję **Zarządzaj uprawnieniami**. W przypadku zadań zarządzania, takich jak zmiana kluczy i adresów URL odpowiedzi, lub aby edytować manifest aplikacji, kliknij pozycję **Zarządzaj aplikacją**.

### <a name="edit-the-apps-settings-or-manifest"></a>Edytuj ustawienia lub manifest aplikacji

Aby edytować ustawienia lub manifest aplikacji, kliknij pozycję **Zarządzaj aplikacją**.

![Strona zarządzanie aplikacją](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [przekazywaniem plików na konto](media-services-portal-upload-files.md).
