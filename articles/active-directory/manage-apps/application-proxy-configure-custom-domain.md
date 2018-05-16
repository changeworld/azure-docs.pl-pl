---
title: Domen niestandardowych w serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zarządzanie domenami niestandardowymi w serwera Proxy aplikacji usługi Azure AD, aby adres URL aplikacji jest taki sam, niezależnie od tego, gdzie użytkownicy do niego dostęp.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cb4620babd3a1ba5087ae9ebd2870c1ef404bb58
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Praca z domenami niestandardowymi w serwera Proxy aplikacji usługi Azure AD

Podczas publikowania aplikacji za pośrednictwem serwera Proxy usługi Azure Active Directory aplikacji, możesz utworzyć zewnętrznego adresu URL dla użytkowników, aby przejść do podczas pracy zdalnej. Ten adres URL pobiera domyślną domenę *yourtenant.msappproxy.net*. Na przykład opublikowaniu aplikacji o nazwie kosztów i dzierżawy jest o nazwie Contoso, a następnie będzie zewnętrzny adres URL https://expenses-contoso.msappproxy.net. Jeśli chcesz korzystać z własnej nazwy domeny, skonfiguruj niestandardową domenę na potrzeby aplikacji. 

Zaleca się skonfigurowanie domeny niestandardowej dla aplikacji, jeśli to możliwe. Oto niektóre zalety domen niestandardowych:

- Użytkownicy przejść do aplikacji z tego samego adresu URL, czy działają wewnątrz lub na zewnątrz sieci.
- Jeśli wszystkie aplikacje tego samego wewnętrzne i zewnętrzne adresy URL, linki w jednej aplikacji, które wskazują na inny nadal działać nawet spoza sieci firmowej. 
- Kontrolowanie znakowanie i tworzyć adresy URL. 


## <a name="configure-a-custom-domain"></a>Konfigurowanie domeny niestandardowej

### <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem niestandardową domenę, upewnij się, że masz następujące wymagania przygotowany: 
- A [zweryfikowaną domeną dodane do usługi Azure Active Directory](../add-custom-domain.md).
- Niestandardowe certyfikat dla domeny, w formie pliku PFX. 
- Aplikację lokalną [opublikowana przy użyciu serwera Proxy aplikacji](application-proxy-publish-azure-portal.md).

### <a name="configure-your-custom-domain"></a>Skonfiguruj domenę niestandardową

Jeśli te wymagania trzy gotowy, wykonaj następujące kroki, aby skonfigurować domenę niestandardową:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** i wybierz aplikację, którymi chcesz zarządzać.
3. Wybierz **serwera Proxy aplikacji**. 
4. W polu zewnętrzny adres URL użyj listy rozwijanej, aby wybrać domenę niestandardową. Jeśli nie widzisz domeny na liście, następnie go nie zostało zweryfikowane jeszcze. 
5. Wybierz **Zapisz**
5. **Certyfikatu** staje się aktywny pola, które zostało wyłączone. Zaznaczenie tego pola. 

   ![Kliknij, aby przekazać certyfikat](./media/application-proxy-configure-custom-domain/certificate.png)

   Jeśli już przekazany certyfikat dla tej domeny, pole certyfikatu Wyświetla informacje o certyfikacie. 

6. Przekazywanie certyfikatu PFX, a następnie wprowadź hasło dla certyfikatu. 
7. Wybierz **zapisać** Aby zapisać zmiany. 
8. Dodaj [rekord DNS](../../dns/dns-operations-recordsets-portal.md) który przekierowuje do domeny msappproxy.net nowego zewnętrznego adresu URL. 

>[!TIP] 
>Należy przekazać jeden certyfikat dla domeny niestandardowej. Po przekazaniu certyfikatu można domeny niestandardowej podczas publikowania nowej aplikacji co wymaga dodatkowej konfiguracji, z wyjątkiem rekordu DNS. 

## <a name="manage-certificates"></a>Zarządzanie certyfikatami

### <a name="certificate-format"></a>Format certyfikatu
Nie podlega ograniczeniom certyfikat metodach podpisu. Obsługiwane są wszystkie kryptografii krzywej eliptycznej (ECC), alternatywnej nazwy podmiotu (SAN) i inne popularne typy certyfikatów. 

Można użyć certyfikatu symboli wieloznacznych, tak długo, jak symbol wieloznaczny odpowiada żądanego zewnętrznego adresu URL. 

### <a name="changing-the-domain"></a>Zmiana domeny
Wszystkie zweryfikowanych domen są wyświetlane na liście rozwijanej zewnętrzny adres URL aplikacji. Aby zmienić domeny, aktualizując pola dla aplikacji. Jeśli chcesz domeny nie ma na liście [Dodaj go jako zweryfikowanej domeny](../add-custom-domain.md). W przypadku wybrania domeny, która nie ma certyfikatu skojarzonego jeszcze, wykonaj kroki 5-7, aby dodać certyfikat. Następnie upewnij się, że aktualizacja rekordu DNS, aby przekierować z nowego zewnętrznego adresu URL. 

### <a name="certificate-management"></a>Zarządzanie certyfikatami
Tego samego certyfikatu można użyć dla wielu zastosowań, chyba że hoście zewnętrznym udostępniać aplikacje. 

Zostanie wyświetlone ostrzeżenie po wygaśnięciu certyfikatu informujący, aby przekazać nowy certyfikat za pośrednictwem portalu. Jeśli certyfikat jest odwołany, użytkownicy mogą pojawić ostrzeżenie o zabezpieczeniach podczas uzyskiwania dostępu do aplikacji. Nie możemy wykonać sprawdzanie odwołań certyfikatów.  Aby zaktualizować certyfikat dla danej aplikacji, przejdź do aplikacji i wykonaj kroki 5-7 do konfigurowania domeny niestandardowe na opublikowanych aplikacji, aby przekazać nowy certyfikat. W przypadku stary certyfikat nie jest używany przez inne aplikacje, zostaną usunięte automatycznie. 

Wszystkie zarządzania certyfikatami jest obecnie stronach poszczególnych aplikacji, należy do zarządzania certyfikatami w kontekście odpowiednie aplikacje. 

## <a name="next-steps"></a>Kolejne kroki
* [Włącz rejestrację jednokrotną](application-proxy-configure-single-sign-on-with-kcd.md) do aplikacji opublikowanych przy użyciu uwierzytelniania usługi Azure AD.
* [Włącz dostęp warunkowy](application-proxy-integrate-with-sharepoint-server.md) do opublikowanej aplikacji.
* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../add-custom-domain.md)


