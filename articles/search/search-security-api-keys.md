---
title: Tworzenie i zarządzanie bezpiecznego administratora, jak i zapytania api KEY dla usługi Azure Search | Dokumentacja firmy Microsoft
description: klucze interfejsu API kontroli dostępu do punktu końcowego usługi. Klucze administratora przyznają dostęp do zapisu. Klucze zapytania mogą być tworzone na dostęp tylko do odczytu.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 4215795b7cd2a25427a3ce9b3cde16bfc69cb009
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185947"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Tworzenie i zarządzanie nimi klucze interfejsu api dla usługi Azure Search

Wszystkie żądania do usługi wyszukiwania należy klucz interfejsu api wygenerowany specjalnie dla usługi. Ten klucz interfejsu api jest jedynym mechanizmu uwierzytelniania dostępu do punktu końcowego usługi wyszukiwania. 

Klucz interfejsu api to ciąg składający się z losowo generowany liter i cyfr. Za pomocą [uprawnienia oparte na rolach](search-security-rbac.md), możesz usunąć lub odczytu kluczy, ale nie można zastąpić klucz przy użyciu hasła użytkownika lub użyć usługi Active Directory jako metody uwierzytelniania podstawowego do uzyskiwania dostępu do operacji wyszukiwania. 

Dwa typy klucze są używane do uzyskania dostępu do usługi wyszukiwania: admin (odczytu i zapisu) i zapytania (tylko do odczytu).

|Klucz|Opis|Limity|  
|---------|-----------------|------------|  
|Jednostka administracyjna|Przyznaje pełne prawa do wszystkich operacji, łącznie z możliwością zarządzania usługą oraz tworzenia i usuwania indeksów, indeksatorów i źródeł danych.<br /><br /> Dwa klucze administratora, określany jako *głównej* i *dodatkowej* klucze w portalu, są generowane, gdy usługa jest tworzony i można osobno ponownie wygenerowane na żądanie. Mając dwa klucze umożliwia przerzucane jednego klucza podczas używania drugi klucz dla nieprzerwanego dostępu do usługi.<br /><br /> Kluczy administratora są tylko określone w nagłówkach żądania HTTP. Klucz interfejsu api administratora nie można umieścić w adresie URL.|Maksymalnie 2 dla usługi|  
|Zapytanie|Zezwala na dostęp tylko do odczytu do indeksów oraz dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.<br /><br /> Klucze zapytania są tworzone na żądanie. Można je utworzyć ręcznie w portalu lub programowo przy użyciu [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klucze zapytania można określić w nagłówku żądania HTTP do wyszukiwania, sugestię lub operacji wyszukiwania. Alternatywnie można przekazać klucz zapytania jako parametr przy użyciu adresu URL. W zależności od tego, jak aplikacja kliencka formulates żądania może być łatwiejsze do przekazania klucza jako parametr zapytania:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 dla usługi|  

 Efekty wizualne nie ma ma różnicy między klucz administratora i klucz zapytania. Oba klucze są ciągi składa się z 32 losowo wygenerowany znaków alfanumerycznych. Jeśli zgubisz track określono typ klucza w aplikacji, możesz [Sprawdź wartości klucza w portalu](https://portal.azure.com) lub użyj [interfejsu API REST](https://docs.microsoft.com/rest/api/searchmanagement/) aby zwrócić wartości i typ klucza.  

> [!NOTE]  
>  Jest on uznawany za rozwiązanie w zakresie zabezpieczeń niską do przekazywania poufnych danych, takich jak `api-key` w identyfikatorze URI żądania. Z tego powodu usługa Azure Search akceptuje tylko klucz zapytania jako `api-key` w zapytaniu ciąg, na które należy unikać chyba, że zawartość indeksu powinny być publicznie dostępne w ten sposób. Ogólną zasadą jest, zalecamy przekazywanie Twojej `api-key` jako nagłówek żądania.  

## <a name="find-api-keys-for-your-service"></a>Znajdowanie kluczy interfejsu api usługi

Możesz uzyskać dostępu do kluczy w portalu lub za pomocą [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/). Aby uzyskać więcej informacji, zobacz [administratora i zapytań klucze interfejsu api zarządzania](search-security-api-keys.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Lista [usługi wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dla Twojej subskrypcji.
3. Wybierz usługę, a następnie odnaleźć na stronie usługi **ustawienia** >**klucze** do wyświetlania kluczy administratora, jak i zapytania.

![Sekcję klucze strony portalu, ustawienia](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Ponowne generowanie kluczy administratora

Dwa klucze administratora są tworzone dla każdej usługi, dzięki czemu można przerzucić klucz podstawowy przy użyciu klucza pomocniczego dla nieprzerwanego dostępu.

W tym samym czasie ponownego generowania kluczy głównych i dodatkowych, wszystkie aplikacje przy użyciu albo klucza do uzyskiwania dostępu do operacji usługi będzie już dostępu do usługi.

1. W **ustawienia** >**klucze** strony, skopiuj klucz pomocniczy.
2. Dla wszystkich aplikacji należy zaktualizować ustawienia klucza interfejsu api, aby używać klucza pomocniczego.
3. Wygeneruj ponownie klucz podstawowy.
4. Zaktualizuj wszystkie aplikacje, aby użyć nowego klucza podstawowego.

## <a name="secure-api-keys"></a>Klucze zabezpieczeń interfejsu api
Bezpieczeństwo kluczy jest zapewniana przez ograniczenie dostępu za pośrednictwem portalu lub interfejsy usługi Resource Manager (programu PowerShell lub interfejsu wiersza polecenia). Jak wspomniano, Administratorzy subskrypcji można wyświetlać i ponowne wygenerowanie wszystkich kluczy interfejsu api. Ze względów Przejrzyj przypisań ról, aby zrozumieć, kto ma dostęp do kluczy administratora.

+ Na pulpicie nawigacyjnym usługi kliknij **(IAM) kontroli dostępu** Aby wyświetlić przypisania roli dla usługi.

Elementy członkowskie następujących ról można wyświetlać i ponowne generowanie kluczy: właściciela, współautora, [współautorzy usługi wyszukiwania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Oparte na tożsamości dostępu za pośrednictwem wyniki wyszukiwania można utworzyć filtrów zabezpieczeń, aby przyciąć wyników przez tożsamość, usuwanie dokumentów, dla których obiekt żądający nie powinny mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) i [bezpiecznego z usługą Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Zobacz także

+ [Kontrola dostępu oparta na rolach w usłudze Azure Search](search-security-rbac.md)
+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Artykuł wydajności i optymalizacji](search-performance-optimization.md)