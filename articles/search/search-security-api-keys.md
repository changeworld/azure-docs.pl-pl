---
title: Tworzenie, zarządzanie i zabezpieczanie administratora i zapytania klucze api KEY dla usługi Azure Search | Dokumentacja firmy Microsoft
description: klucze interfejsu API kontrola dostępu do punktu końcowego usługi. Klucze administratora przyznają dostęp do zapisu. Aby uzyskać dostęp tylko do odczytu można tworzyć klucze zapytania.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: heidist
ms.openlocfilehash: 0a8975fad21645535a6e7407bcd2d59f537a9fff
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727977"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Tworzenie i zarządzanie nimi klucze api KEY dla usługi Azure Search

Wszystkie żądania do usługi wyszukiwania należy tylko do odczytu klucza api-key wygenerowany specjalnie dla Twojej usługi. Klucz interfejsu api jest jedynym mechanizmu uwierzytelniania dostępu do punktu końcowego usługi wyszukiwania i muszą być zawarte na każde żądanie. W [rozwiązania REST](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key), klucz interfejsu api jest zazwyczaj określana w nagłówku żądania. W [rozwiązań .NET](search-howto-dotnet-sdk.md#core-scenarios), klucz jest często określony jako ustawienie konfiguracji, a następnie przekazywany jako [poświadczenia](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (klucz administratora) lub [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (klucz zapytania) na [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Klucze są tworzone przy użyciu usługi wyszukiwania podczas inicjowania obsługi usługi. Możesz wyświetlać i uzyskiwanie wartości klucza [witryny Azure portal](https://portal.azure.com).

![Strony w portalu, ustawień kluczy sekcji](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Co to jest klucz interfejsu api

Klucz interfejsu api to ciąg składający się z liter i cyfr generowany losowo. Za pomocą [uprawnień opartych na rolach](search-security-rbac.md), można usunąć lub odczytu kluczy, ale nie można wymienić klucz przy użyciu zdefiniowanych przez użytkownika hasła lub użyć usługi Active Directory jako metody uwierzytelniania podstawowego do uzyskiwania dostępu do operacji wyszukiwania. 

Dwa typy kluczy są używane do uzyskania dostępu do usługi wyszukiwania: administrator (odczyt zapis) i zapytania (tylko do odczytu).

|Klucz|Opis|Limity|  
|---------|-----------------|------------|  
|Jednostka administracyjna|Przyznaje pełne prawa do wszystkich operacji, w tym możliwość zarządzania usługą oraz tworzenia i usuwania indeksów, indeksatorów i źródeł danych.<br /><br /> Dwa klucze administratora, nazywane *podstawowego* i *dodatkowej* klucze w witrynie portal, są generowane, gdy usługa zostanie utworzona i może zostać indywidualnie wygenerowany ponownie na żądanie. Mając dwa klucze umożliwia przechodzą z jednego klucza podczas korzystania z drugiego klucza opinię dotyczącą przedłużenia dostępu do usługi.<br /><br /> Klucze administratora są tylko określone w nagłówkach żądań HTTP. Klucz interfejsu api administratora nie można umieścić w adresie URL.|Maksymalnie 2 na usługę|  
|Zapytanie|Przyznanie dostępu tylko do odczytu do indeksów i dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.<br /><br /> Klucze zapytania są tworzone na żądanie. Możesz utworzyć je ręcznie w portalu lub programowo przy użyciu [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klucze zapytania można określić w nagłówku żądania HTTP do wyszukiwania, sugestię lub operacji wyszukiwania. Alternatywnie można przekazać klucz zapytania jako parametr adresu URL. W zależności od tego, jak Twoja aplikacja kliencka formulates żądania może być łatwiejsze do przekazania klucza jako parametru zapytania:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 na usługę|  

 Wizualnie nie ma różnicy między klucz administratora lub klucz zapytania. Oba klucze są ciągi składające się z 32 losowo generowany znaków alfanumerycznych. Jeśli zgubisz śledzenie jakiego rodzaju klucza jest określona w aplikacji, możesz to zrobić [sprawdzić wartości klucza w portalu](https://portal.azure.com) lub użyj [interfejsu API REST](https://docs.microsoft.com/rest/api/searchmanagement/) zwracać wartości i typ klucza.  

> [!NOTE]  
>  Chodzi w zakresie zabezpieczeń niską do przekazywania poufnych danych, takich jak `api-key` w identyfikatorze URI żądania. Z tego powodu usługa Azure Search akceptuje tylko klucz zapytania jako `api-key` w zapytaniu ciągu, na które należy unikać sposób, chyba że zawartość indeksu powinny być dostępne publicznie. Ogólną zasadą jest, zalecamy przekazywanie Twojego `api-key` jako nagłówek żądania.  

## <a name="find-api-keys-for-your-service"></a>Znajdź klucze api KEY dla usługi

Możesz uzyskać klucze dostępu w portalu lub za pomocą [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/). Aby uzyskać więcej informacji, zobacz [Zarządzanie administratora i zapytania klucze api Key](search-security-api-keys.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Lista [usługi wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dla Twojej subskrypcji.
3. Wybierz usługę, a następnie na stronie usługi, znalezienia **ustawienia** >**klucze** do wyświetlania kluczy administratora i zapytania.

![Strony w portalu, ustawień kluczy sekcji](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Wygeneruj ponownie klucze administratora

Dwa klucze administratora są tworzone dla każdej usługi, aby można było przerzucania klucza podstawowego, przy użyciu klucza pomocniczego opinię dotyczącą przedłużenia dostępu.

Jeśli w tym samym czasie można ponownie wygenerować kluczy podstawowych i pomocniczych, w aplikacjach używających żadnego z nich do uzyskiwania dostępu do operacji usługi nie jest już mają dostęp do usługi.

1. W **ustawienia** >**klucze** stronie, skopiuj klucz pomocniczy.
2. Dla wszystkich aplikacji należy zaktualizować ustawienia klucza interfejsu api, aby używać klucza pomocniczego.
3. Wygeneruj ponownie klucz podstawowy.
4. Zaktualizuj wszystkie aplikacje, aby przy użyciu nowego klucza podstawowego.

## <a name="secure-api-keys"></a>Zabezpieczanie kluczy interfejsu api
Zabezpieczeń klucza jest zapewniana przez ograniczenie dostępu za pośrednictwem portalu lub interfejsów Menedżera zasobów (programu PowerShell lub interfejsu wiersza polecenia). Jak wspomniano, administratorów subskrypcji można przeglądać i ponowne wygenerowanie wszystkich kluczy interfejsu api. Ze względów Przejrzyj przypisania ról, aby dowiedzieć się, kto ma dostęp do kluczy administratora.

+ Na pulpicie nawigacyjnym usługi kliknij **kontrola dostępu (IAM)** i następnie **przypisań ról** kartę, aby wyświetlić przypisania roli dla usługi.

Członkami następujących ról można wyświetlić lub ponownie wygenerować klucze: właściciel, współautor, [współautorów usługi wyszukiwania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> W przypadku dostępu na podstawie tożsamości za pośrednictwem wyników wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwanie dokumentów, dla których obiekt żądający nie powinny mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) i [bezpiecznego z usługą Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Zobacz także

+ [Kontrola dostępu oparta na rolach w usłudze Azure Search](search-security-rbac.md)
+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Artykuł wydajność i optymalizacja](search-performance-optimization.md)