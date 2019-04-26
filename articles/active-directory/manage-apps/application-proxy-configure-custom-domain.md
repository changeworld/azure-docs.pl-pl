---
title: Domeny niestandardowe na serwerze Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zarządzanie domenami niestandardowymi na serwer Proxy aplikacji usługi Azure AD, aby adres URL aplikacji jest taki sam, niezależnie od tego, gdzie użytkownikom uzyskać do niego dostęp.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59663346fce415d73609f09345048ff321f1a234
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293544"
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD

Podczas publikowania aplikacji za pośrednictwem serwera Proxy usługi Azure Active Directory Application, możesz utworzyć zewnętrzny adres URL dla użytkowników, aby odwiedzić, gdy działają w zdalnie. Ten adres URL pobiera domyślnej domeny *yourtenant.msappproxy.net*. Na przykład, jeśli opublikowano aplikację o nazwie wydatki i dzierżawy jest o nazwie Contoso, a następnie zewnętrzny adres URL będzie `https://expenses-contoso.msappproxy.net`. Jeśli chcesz korzystać z własnej nazwy domeny, należy skonfigurować domeny niestandardowej na potrzeby aplikacji. 

Zaleca się, że Konfigurowanie domen niestandardowych dla aplikacji, jeśli to możliwe. Oto niektóre korzyści domen niestandardowych:

- Użytkownicy będą mogli uzyskać do aplikacji przy użyciu tego samego adresu URL, czy działają wewnątrz lub na zewnątrz sieci.
- Jeśli wszystkie aplikacje mają tych samych adresów URL wewnętrznych i zewnętrznych, łącza w jednej aplikacji, które wskazują na inny nadal działać nawet spoza sieci firmowej. 
- Ty określasz, znakowanie i tworzyć adresy URL mają. 


## <a name="configure-a-custom-domain"></a>Konfigurowanie domeny niestandardowej

### <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem domenę niestandardową, upewnij się, że masz następujące wymagania, które są przygotowywane: 
- A [zweryfikowaną domenę dodane do usługi Azure Active Directory](../fundamentals/add-custom-domain.md).
- Niestandardowy certyfikat dla domeny, w postaci pliku PFX. 
- Aplikację w środowisku lokalnym [opublikowane za pośrednictwem serwera Proxy aplikacji](application-proxy-add-on-premises-application.md).

### <a name="configure-your-custom-domain"></a>Konfigurowanie domeny niestandardowej

Jeśli masz te trzy wymagania gotowe, wykonaj następujące kroki, aby skonfigurować domenę niestandardową:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** i wybierz aplikację, którą chcesz zarządzać.
3. Wybierz **serwera Proxy aplikacji**. 
4. W polu adres URL zewnętrznego Użyj listy rozwijanej, aby wybrać domenę niestandardową. Jeśli nie widzisz swojej domeny na liście, następnie go nie zostało zweryfikowane jeszcze. 
5. Wybierz **Zapisz**
5. **Certyfikatu** staje się aktywny pola, które zostało wyłączone. Zaznaczenie tego pola. 

   ![Kliknij, aby przekazać certyfikat](./media/application-proxy-configure-custom-domain/certificate.png)

   Jeśli przekazano już certyfikat dla tej domeny, pole certyfikatu dotyczy Wyświetla informacje o certyfikacie. 

6. Przekazywanie certyfikatu PFX, a następnie wprowadź hasło dla certyfikatu. 
7. Wybierz **Zapisz** Aby zapisać zmiany. 
8. Dodaj [rekordu DNS](../../dns/dns-operations-recordsets-portal.md) który przekierowuje nowy zewnętrzny adres URL do domeny msappproxy.net.
9. Sprawdź, czy rekord DNS jest skonfigurowany poprawnie za pomocą [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) polecenie, aby zobaczyć, jeśli zewnętrzny adres URL jest osiągalny i domeny msapproxy.net pojawia się jako alias.

>[!TIP] 
>Wystarczy przekazać jeden certyfikat dla domeny niestandardowej. Po przekazaniu certyfikatu, możesz wybrać domeny niestandardowej podczas publikowania nowej aplikacji i nie trzeba wykonać dodatkowe czynności konfiguracyjne z wyjątkiem rekordu DNS. 

## <a name="manage-certificates"></a>Zarządzanie certyfikatami

### <a name="certificate-format"></a>Format certyfikatu
Nie ma żadnych ograniczeń w metodach podpisu certyfikatu. Obsługiwane są wszystkie kryptografii krzywej eliptycznej (ECC), alternatywnej nazwy podmiotu (SAN) i innych typowych typów certyfikatów. 

Można użyć certyfikatu symboli wieloznacznych, tak długo, jak symbol wieloznaczny pasuje do żądanego zewnętrznego adresu URL. 

### <a name="changing-the-domain"></a>Zmiana domeny
Wszystkie zweryfikowanych domen są wyświetlane na liście rozwijanej zewnętrzny adres URL aplikacji. Aby zmienić domenę, wystarczy zaktualizować pola dla aplikacji. Jeśli domeny, nie ma na liście [dodać je jako zweryfikowane domeny](../fundamentals/add-custom-domain.md). Po wybraniu domeny, które nie mają certyfikatu skojarzonego jeszcze, wykonaj kroki 5 – 7, aby dodać certyfikat. Następnie upewnij się, że aktualizujesz rekord DNS, aby przekierować z nowego zewnętrznego adresu URL. 

### <a name="certificate-management"></a>Zarządzanie certyfikatami
Można użyć tego samego certyfikatu dla wielu aplikacji, chyba że aplikacje udostępniania zewnętrznego hosta. 

Zostanie wyświetlone ostrzeżenie, jeśli certyfikat wygaśnie, informujący, aby przekazać nowy certyfikat za pośrednictwem portalu. Jeśli certyfikat jest odwołany, użytkownicy mogą otrzymywać ostrzeżenie o zabezpieczeniach podczas uzyskiwania dostępu do aplikacji. Firma Microsoft nie wykonują sprawdzanie odwołania certyfikatów.  Aby zaktualizować certyfikat dla danej aplikacji, przejdź do aplikacji, a następnie wykonaj kroki 5 – 7 konfigurowania domen niestandardowych w opublikowanych aplikacji, aby przekazać nowy certyfikat. Jeśli stary certyfikat nie jest używany przez inne aplikacje, są usuwane automatycznie. 

Obecnie wszystkich zarządzania certyfikatami jest stronach poszczególnych aplikacji, dzięki czemu potrzebne do zarządzania certyfikatami w kontekście odpowiednie aplikacje. 

## <a name="next-steps"></a>Kolejne kroki
* [Włącz logowanie jednokrotne](application-proxy-configure-single-sign-on-with-kcd.md) do aplikacji opublikowanych przy użyciu uwierzytelniania usługi Azure AD.
* [Włącz dostęp warunkowy](application-proxy-integrate-with-sharepoint-server.md) do opublikowanej aplikacji.
* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md)


