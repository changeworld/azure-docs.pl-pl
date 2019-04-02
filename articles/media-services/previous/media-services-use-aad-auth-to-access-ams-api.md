---
title: Dostęp do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Poznaj koncepcje i kroki, aby korzystać z usługi Azure Active Directory (Azure AD) do uwierzytelniania dostępu do interfejsu API usługi Azure Media Services.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: d80a58f1886ecc1ca2a735881fc5822f2fc0c53b
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802494"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Interfejsu API usługi Azure Media Services to API typu RESTful. Służy do wykonywania operacji na zasoby multimediów, za pomocą interfejsu API REST lub przy użyciu zestawów SDK klienta dostępne. Usługa Azure Media Services oferuje zestaw SDK klienta usługi Media Services dla programu Microsoft .NET. Autoryzowanie dostępu do zasobów usługi Media Services i interfejsu API usług Media Services, użytkownik musi najpierw zostać uwierzytelnione. 

Usługa Media Services obsługuje [usługi Azure Active Directory (Azure AD) — uwierzytelnianie oparte na](../../active-directory/fundamentals/active-directory-whatis.md). Usługa Azure Media REST wymaga, czy użytkownik lub aplikacja, która sprawia, że interfejs API REST żądań, jeden **Współautor** lub **właściciela** roli dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [wprowadzenie opartej na rolach kontrola dostępu w witrynie Azure portal](../../role-based-access-control/overview.md).  

Ten dokument zawiera omówienie sposobu dostępu do interfejsu API usługi Media za pomocą REST lub interfejsów API platformy .NET.

> [!NOTE]
> Autoryzację kontroli dostępu została zakończona w dniu 1 czerwca 2018 r.

## <a name="access-control"></a>Kontrola dostępu

Dla żądania REST multimediów Azure została wykonana pomyślnie wywoływania użytkownik musi mieć rolę współautora lub właściciela dla konta usługi Media Services, który próbuje uzyskać dostęp.  
Tylko użytkownik mający rolę właściciela umożliwiają dostęp do zasobów (konto) nośnika do nowych użytkowników lub aplikacji. Roli współautora mogą korzystać tylko z zasobu multimediów.
Nieautoryzowane żądania zakończyć się niepowodzeniem z kodem stanu 401. Jeśli widzisz tego kodu błędu, sprawdź, czy użytkownik ma rolę współautora lub właściciela, przypisany do konta usługi Media Services. Możesz to sprawdzić w witrynie Azure portal. Wyszukaj kontem multimediów, a następnie kliknij przycisk **kontroli dostępu** kartę. 

![Karty Kontrola dostępu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy uwierzytelniania 
 
Gdy używasz uwierzytelniania usługi Azure AD za pomocą usługi Azure Media Services, masz dwie opcje uwierzytelniania:

- **Uwierzytelnianie użytkownika**. Uwierzytelnianie osobę, która jest za pomocą aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja najpierw powinien zostać wyświetlony monit użytkownika o poświadczenia użytkownika. Przykładem jest aplikacja do konsoli zarządzania używany przez użytkowników autoryzowanych do monitorowania zadań kodowania lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie jednostki usługi**. Uwierzytelnianie usługi. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które działają usługi demona, usługi warstwy środkowej lub zaplanowanych zadań. Przykłady to aplikacje sieci web, aplikacji funkcji, logic apps, API i mikrousług.

### <a name="user-authentication"></a>Uwierzytelnianie użytkowników 

Aplikacje, które powinny używać metody uwierzytelniania użytkownika są zarządzania i monitorowania aplikacji natywnych: aplikacje mobilne, aplikacje Windows i aplikacji konsoli. Tego typu rozwiązań jest przydatne, gdy chcesz, aby interakcji z usługą w jednym z następujących scenariuszy:

- Pulpit nawigacyjny do kodowania zadań monitorowania.
- Pulpit nawigacyjny monitorowania dla strumieni na żywo.
- Aplikacji do zarządzania dla użytkowników w komputerze lub urządzeniu przenośnym można administrować zasobami w ramach konta usługi Media Services.

> [!NOTE]
> Ta metoda uwierzytelniania nie powinny służyć do aplikacji dla użytkowników. 

Natywna aplikacja musi najpierw uzyskać token dostępu z usługi Azure AD, a następnie użyć go po wprowadzeniu żądania HTTP do interfejsu API REST usługi Media Services. Dodaj token dostępu do żądanego nagłówka. 

Na poniższym diagramie przedstawiono przepływ uwierzytelniania Typowa aplikacja interakcyjne: 

![Aplikacje natywne w diagramie](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Na powyższym diagramie liczby reprezentują przepływ żądań w porządku chronologicznym.

> [!NOTE]
> Użycie metody uwierzytelniania użytkownika, wszystkie aplikacje współużytkować ten sam identyfikator klienta aplikacji natywnej (ustawienie domyślne) i identyfikator URI przekierowania aplikacji natywnej. 

1. Monituj użytkownika o poświadczenia.
2. Żądanie tokenu dostępu usługi Azure AD z następującymi parametrami:  

   * Punkt końcowy dzierżawy usługi Azure AD.

       W witrynie Azure portal można pobrać informacji o dzierżawie. Umieść kursor na nazwie zalogowanego użytkownika w prawym górnym rogu.
   * Identyfikator URI zasobu usługi Media Services. 

       Ten identyfikator URI jest taka sama dla konta usługi Media Services, które znajdują się w tym samym środowisku platformy Azure (na przykład https://rest.media.azure.net).

   * Identyfikator klienta aplikacji Media Services (macierzysty).
   * Identyfikator URI przekierowania aplikacji Media Services (macierzysty).
   * Identyfikator URI dla usługi Media Services REST zasobu.
        
       Identyfikator URI reprezentuje punkt końcowy interfejsu API REST (na przykład https://test03.restv2.westus.media.azure.net/api/).

     Aby uzyskać wartości dla tych parametrów, zobacz [użyj witryny Azure portal, aby uzyskać dostęp ustawienia uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md) przy użyciu opcji Uwierzytelnianie użytkownika.

3. Token dostępu usługi Azure AD są wysyłane do klienta.
4. Klient wysyła żądanie do interfejsu API REST usługi Azure Media przy użyciu tokenu dostępu usługi Azure AD.
5. Klient otrzymuje dane z usługi Media Services.

Aby uzyskać informacje o sposobie używania uwierzytelniania usługi Azure AD do komunikacji z żądania REST przy użyciu klienta usługi Media Services .NET SDK, zobacz [uwierzytelnianie usługi Azure AD dostęp do interfejsu API usługi multimediów za pomocą platformy .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz zestawu SDK klienta platformy .NET usług Media Services, należy ręcznie utworzyć żądanie tokenu dostępu do usługi Azure AD przy użyciu parametrów opisanych w kroku 2. Aby uzyskać więcej informacji, zobacz [jak uzyskiwanie tokenu usługi Azure AD za pomocą usługi Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które działają usługi warstwy środkowej i zaplanowane zadania: sieci web, aplikacji, aplikacje funkcji, logic apps, interfejsów API i mikrousług. Ta metoda uwierzytelniania jest również odpowiedni dla aplikacji interaktywnych, w których możesz chcieć użyć konta usługi do zarządzania zasobami.

Gdy używasz metody uwierzytelniania jednostki usługi do tworzenia scenariusze konsumenckie uwierzytelniania zwykle odbywa się w warstwie środkowej (za pośrednictwem niektórych interfejsu API), a nie bezpośrednio w aplikacji mobilnej lub klasycznej. 

Aby użyć tej metody, utworzyć jednostkę w jego własnej dzierżawy aplikacji usługi Azure AD i usługi. Po utworzeniu aplikacji, udzielić aplikacji dostępu do roli współautora lub właściciela do konta usługi Media Services. Można w tym w witrynie Azure portal przy użyciu wiersza polecenia platformy Azure lub za pomocą skryptu programu PowerShell. Możesz również użyć istniejącej aplikacji usługi Azure AD. Można rejestrować i zarządzania aplikacji usługi Azure AD i jednostkę usługi [w witrynie Azure portal](media-services-portal-get-started-with-aad.md). Możesz również można to zrobić za pomocą [wiersza polecenia platformy Azure](media-services-use-aad-auth-to-access-ams-api.md) lub [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplikacje warstwy środkowej](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po utworzeniu aplikacji usługi Azure AD, można uzyskać wartości dla następujących ustawień. Te wartości będą potrzebne do uwierzytelniania:

- Identyfikator klienta 
- Klucz tajny klienta 

Na poprzednim rysunku liczby reprezentują przepływ żądań w kolejności chronologicznej:
    
1. Aplikacja warstwy środkowej, (interfejs API sieci web lub aplikacji sieci web) żąda tokenu dostępu usługi Azure AD, która ma następujące parametry:  

   * Punkt końcowy dzierżawy usługi Azure AD.

       W witrynie Azure portal można pobrać informacji o dzierżawie. Umieść kursor na nazwie zalogowanego użytkownika w prawym górnym rogu.
   * Identyfikator URI zasobu usługi Media Services. 

       Ten identyfikator URI jest taka sama dla konta usługi Media Services, które znajdują się w tym samym środowisku platformy Azure (na przykład https://rest.media.azure.net).

   * Identyfikator URI dla usługi Media Services REST zasobu.

       Identyfikator URI reprezentuje punkt końcowy interfejsu API REST (na przykład https://test03.restv2.westus.media.azure.net/api/).

   * Wartości aplikacji w usłudze Azure AD: identyfikator klienta oraz klucz tajny klienta.
    
     Aby uzyskać wartości dla tych parametrów, zobacz [użyj witryny Azure portal, aby uzyskać dostęp ustawienia uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md) przy użyciu opcji Uwierzytelnianie jednostki usługi.

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwa środkowa wysyła żądanie do interfejsu API REST usługi Azure Media z tokenem usługi Azure AD.
5. Warstwa środkowa otrzymuje dane z usługi Media Services.

Aby uzyskać więcej informacji na temat korzystania z uwierzytelniania usługi Azure AD do komunikacji z żądania REST przy użyciu klienta usługi Media Services .NET SDK, zobacz [uwierzytelnianie usługi Azure AD dostęp do interfejsu API usługi multimediów Azure przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz zestawu SDK klienta platformy .NET usług Media Services, należy ręcznie utworzyć żądanie tokenu usługi Azure AD przy użyciu parametrów opisanych w kroku 1. Aby uzyskać więcej informacji, zobacz [jak uzyskiwanie tokenu usługi Azure AD za pomocą usługi Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Wyjątek: "Serwer zdalny zwrócił błąd: (401) nieautoryzowane."

Rozwiązanie: Dla żądania REST usługi Media zakończyło się sukcesem użytkownika wywołującego musi być roli współautora lub właściciela w ramach konta usługi Media Services, który próbuje uzyskać dostęp. Aby uzyskać więcej informacji, zobacz [kontroli dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control) sekcji.

## <a name="resources"></a>Zasoby

Omówienie pojęć dotyczących uwierzytelniania usługi Azure AD są następujące artykuły: 

- [Scenariusze uwierzytelniania obsługiwane przez usługi Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Dodawanie, aktualizowanie lub usuwanie aplikacji w usłudze Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Konfigurowanie i zarządzanie kontrolą dostępu opartą na rolach przy użyciu programu PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Kolejne kroki

* Użyj portalu Azure w celu [uwierzytelniania dostępu do usługi Azure AD, korzystanie z interfejsu API usługi Azure Media Services](media-services-portal-get-started-with-aad.md).
* Uwierzytelnianie usługi Azure AD do [dostęp do interfejsu API usługi multimediów Azure przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md).

