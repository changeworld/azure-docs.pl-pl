---
title: Dostęp do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat pojęć i czynności umożliwiające używa usługi Azure Active Directory (Azure AD) w celu uwierzytelniania dostępu do interfejsu API Azure Media Services.
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
ms.openlocfilehash: 57f2680d6b3f06a88a13a09018e7d72afcb710a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD
 
Azure Media Services API jest interfejs API RESTful. Służy on do wykonywania operacji na zasobów multimedialnych przy użyciu interfejsu API REST lub przy użyciu klienta dostępnych zestawów SDK. Usługa Azure Media Services udostępnia klienta usługi Media Services SDK dla programu Microsoft .NET. Autoryzowanie dostępu do zasobów usługi Media Services i Media Services API, użytkownik musi najpierw zostać uwierzytelniony. 

Usługa Media Services obsługuje [usługi Azure Active Directory (Azure AD)-uwierzytelniania opartego na](../active-directory/active-directory-whatis.md). Usługa Azure Media REST wymaga, czy użytkownik lub aplikacja, która sprawia, że interfejs API REST żądania albo **współautora** lub **właściciela** roli dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [wprowadzenie opartej na rolach kontroli dostępu w portalu Azure](../role-based-access-control/overview.md).  

> [!IMPORTANT]
> Obecnie usługa Media Services obsługuje model uwierzytelniania usługi kontroli dostępu platformy Azure. Jednak na 1 czerwca 2018 zostaną wycofane autoryzacji kontroli dostępu. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

Ten dokument zawiera omówienie sposobu dostęp do interfejsu API usług Media Services przy użyciu przerwę lub interfejsów API architektury .NET.

## <a name="access-control"></a>Kontrola dostępu

Dla żądania usługi Azure Media REST została wykonana pomyślnie wywołujący użytkownik musi mieć rolę współautora lub właściciela dla konta usługi Media Services, które próbujesz uzyskać dostęp.  
Tylko użytkownik mający rolę właściciela zapewnić dostęp do zasobów (konto) nośnika do nowych użytkowników lub aplikacji. Rola współautora ma dostęp tylko do zasobu multimediów.
Nieautoryzowanych żądań zakończyć się niepowodzeniem z kodem stanu 401. Jeśli widzisz tego kodu błędu, należy sprawdzić, czy użytkownika ma rolę współautora lub właściciela przypisane do konta usługi Media Services. Można to sprawdzić w portalu Azure. Wyszukaj konta nośnika, a następnie kliknij przycisk **kontrola dostępu** kartę. 

![Karta kontroli dostępu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy uwierzytelniania 
 
Korzystając z uwierzytelniania usługi Azure AD z usługi Azure Media Services, masz dwie opcje uwierzytelniania:

- **Uwierzytelnianie użytkownika**. Uwierzytelnianie osoby, która korzysta z aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja powinna pierwszy monit o podanie poświadczeń użytkownika. Przykładem jest aplikacja konsoli zarządzania używane przez autoryzowanych użytkowników do monitorowania kodowania zadań lub transmisja strumieniowa na żywo. 
- **Uwierzytelnianie głównej usługi**. Uwierzytelniania usługi. Aplikacje, które zazwyczaj używają tej metody uwierzytelniania są aplikacji uruchamianych demon usługi, usługi warstwy środkowej lub zaplanowanych zadań. Przykłady są aplikacje sieci web, aplikacje funkcji aplikacji logiki, interfejsu API i mikrousług.

### <a name="user-authentication"></a>Uwierzytelnianie użytkowników 

Aplikacje, które należy użyć metody uwierzytelniania użytkownika są zarządzanie i monitorowanie natywnych aplikacji: aplikacje mobilne, aplikacje systemu Windows i aplikacji konsoli. Tego typu rozwiązanie jest przydatne, gdy chcesz interakcji z usługą w jednym z następujących scenariuszy:

- Pulpit nawigacyjny do kodowania zadań monitorowania.
- Monitorowanie, odwiedź pulpit nawigacyjny strumienie na żywo.
- Aplikacji do zarządzania użytkownikami desktop lub mobile do administrowania zasobami w ramach konta usługi Media Services.

> [!NOTE]
> Nie można używać tej metody uwierzytelniania dla aplikacji dla użytkowników. 

Natywnych aplikacji musi najpierw uzyskać token dostępu z usługi Azure AD, a następnie użyć go po wprowadzeniu żądania HTTP do interfejsu API REST usługi Media. Dodaj token dostępu do nagłówka żądania. 

Na poniższym diagramie przedstawiono przepływ uwierzytelniania typowych aplikacji: 

![Diagram aplikacje natywne](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Na powyższym diagramie liczby reprezentują przepływ żądań w kolejności chronologicznej.

> [!NOTE]
> Korzystając z metody uwierzytelniania użytkownika, wszystkie aplikacje współużytkować ten sam identyfikator klienta aplikacji natywnej (ustawienie domyślne) i identyfikator URI przekierowania aplikacji natywnej. 

1. Monituj użytkownika o poświadczenia.
2. Żądanie tokenu dostępu usługi Azure AD z następującymi parametrami:  

    * Punkt końcowy dzierżawy usługi Azure AD.

        Informacje o dzierżawy można pobrać z portalu Azure. Umieść kursor na nazwę zalogowanego użytkownika w górnym prawym narożniku.
    * Identyfikator URI zasobu usługi Media Services. 

        Ten identyfikator URI jest taki sam dla konta usługi Media Services, które znajdują się w tym samym środowisku platformy Azure (na przykład https://rest.media.azure.net).

    * Identyfikator klienta aplikacji usługi Media Services (macierzysty).
    * Identyfikator URI przekierowania aplikacji usługi Media Services (macierzysty).
    * Identyfikator URI dla usługi REST Media Services zasobu.
        
        Identyfikator URI reprezentuje punkt końcowy interfejsu API REST (na przykład https://test03.restv2.westus.media.azure.net/api/).

    Aby uzyskać wartości dla tych parametrów, zobacz [dostęp do ustawień uwierzytelniania usługi Azure AD za pomocą portalu Azure](media-services-portal-get-started-with-aad.md) przy użyciu opcji uwierzytelniania użytkownika.

3. Token dostępu usługi Azure AD są wysyłane do klienta.
4. Klient wysyła żądanie do interfejsu API REST multimediów Azure przy użyciu tokenu dostępu usługi Azure AD.
5. Klient otrzymuje w odpowiedzi dane z usługi Media Services.

Aby uzyskać informacje o sposobie używania uwierzytelniania usługi Azure AD do komunikowania się z żądaniami REST przy użyciu zestawu SDK klienta Media Services na platformie .NET, zobacz [uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usług Media z platformą .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz klienta Media Services na platformie .NET SDK, należy ręcznie utworzyć żądania tokenu dostępu do usługi Azure AD przy użyciu parametrów opisanych w kroku 2. Aby uzyskać więcej informacji, zobacz [Pobierz token programu Azure AD przy użyciu usługi Azure AD Authentication Library](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aplikacje, które zazwyczaj używają tej metody uwierzytelniania są aplikacji uruchamianych usług warstwy środkowej i zaplanowanych zadań: sieci web aplikacji, aplikacje funkcji aplikacji logiki, interfejsów API i mikrousług. Ta metoda uwierzytelniania jest również odpowiedni dla aplikacji interaktywnych, w których można użyć konta usługi do zarządzania zasobami.

Użycie metody uwierzytelniania główną usługi do tworzenia scenariusze konsumenckie uwierzytelniania zwykle odbywa się w warstwy środkowej (za pośrednictwem niektórych interfejsu API), a nie bezpośrednio w aplikacji mobilnych lub komputerowych. 

Aby użyć tej metody, utworzyć podmiot w dzierżawie własną aplikację usługi Azure AD i usługi. Po utworzeniu aplikacji powodują aplikacji dostęp do roli współautora lub właściciela konta usługi Media Services. Możesz zrobić to w portalu Azure za pomocą wiersza polecenia platformy Azure lub za pomocą skryptu programu PowerShell. Można również użyć istniejącej aplikacji usługi Azure AD. Możesz zarejestrować i zarządzanie aplikacji usługi Azure AD i nazwę główną usługi [w portalu Azure](media-services-portal-get-started-with-aad.md). Możesz również można to zrobić przy użyciu [Azure CLI 2.0](media-services-use-aad-auth-to-access-ams-api.md) lub [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplikacje warstwy środkowej](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po utworzeniu aplikacji usługi Azure AD, można uzyskać wartości dla następujących ustawień. Te wartości są wymagane do uwierzytelniania:

- Identyfikator klienta 
- Klucz tajny klienta 

Na powyższej ilustracji liczby reprezentują przepływ żądań w kolejności chronologicznej:
    
1. Aplikacja warstwy środkowej (interfejs API sieci web lub aplikacji sieci web) żądania tokenu dostępu usługi Azure AD, który ma następujące parametry:  

    * Punkt końcowy dzierżawy usługi Azure AD.

        Informacje o dzierżawy można pobrać z portalu Azure. Umieść kursor na nazwę zalogowanego użytkownika w górnym prawym narożniku.
    * Identyfikator URI zasobu usługi Media Services. 

        Ten identyfikator URI jest taki sam dla konta usługi Media Services, które znajdują się w tym samym środowisku platformy Azure (na przykład https://rest.media.azure.net).

    * Identyfikator URI dla usługi REST Media Services zasobu.

        Identyfikator URI reprezentuje punkt końcowy interfejsu API REST (na przykład https://test03.restv2.westus.media.azure.net/api/).

    * Wartości aplikacji w usłudze Azure AD: identyfikator klienta i klucz tajny klienta.
    
    Można pobrać wartości dla tych parametrów, zobacz [dostęp do ustawień uwierzytelniania usługi Azure AD za pomocą portalu Azure](media-services-portal-get-started-with-aad.md) przy użyciu opcji uwierzytelniania główną usługi.

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwy środkowej wysyła żądanie do interfejsu API REST multimediów Azure przy użyciu tokenu usługi Azure AD.
5. Warstwy środkowej ponownie pobiera dane z usługi Media Services.

Aby uzyskać więcej informacji o sposobie używania uwierzytelniania usługi Azure AD do komunikowania się z żądaniami REST przy użyciu zestawu SDK klienta Media Services na platformie .NET, zobacz [uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure z platformą .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz klienta Media Services na platformie .NET SDK, należy ręcznie utworzyć żądania tokenu usługi Azure AD przy użyciu parametrów opisanych w kroku 1. Aby uzyskać więcej informacji, zobacz [Pobierz token programu Azure AD przy użyciu usługi Azure AD Authentication Library](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Wyjątek: "serwer zdalny zwrócił błąd: Brak autoryzacji (401)."

Rozwiązanie: REST usługi Media żądania została wykonana pomyślnie, wywoływania użytkownik musi być współautora lub właściciela roli w ramach konta usługi Media Services, który próbujesz uzyskać dostęp. Aby uzyskać więcej informacji, zobacz [kontrola dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control) sekcji.

## <a name="resources"></a>Zasoby

Omówienie pojęć dotyczących uwierzytelniania usługi Azure AD są następujące artykuły: 

- [Uwierzytelnianie scenariusze związane z usługą Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [Dodawanie, aktualizowanie lub usuwanie aplikacji w usłudze Azure AD](../active-directory/develop/active-directory-integrating-applications.md)
- [Konfigurowanie i zarządzanie nimi kontroli dostępu opartej na rolach przy użyciu programu PowerShell](../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Kolejne kroki

* Użyj portalu Azure do [uwierzytelniania dostępu do usługi Azure AD, aby korzystać z interfejsu API usługi Azure Media Services](media-services-portal-get-started-with-aad.md).
* Uwierzytelnianie usługi Azure AD do [dostępu Azure Media Services API z platformą .NET](media-services-dotnet-get-started-with-aad.md).

