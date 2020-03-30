---
title: Drzwi frontowe platformy Azure — obsługa domen wieloznacznych
description: Ten artykuł ułatwia zrozumienie, w jaki sposób usługi Azure Front Door obsługuje mapowanie domen wieloznacznych i zarządzanie nimi na liście domen niestandardowych
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537442"
---
# <a name="wildcard-domains"></a>Domeny wieloznaczne

Poza domenami i poddomenami wierzchołków można również mapować nazwę domeny z symbolami wieloznacznych na listę hostów frontendu lub domen niestandardowych profilu drzwi frontowych. Posiadanie domen wieloznacznych w konfiguracji drzwiach frontowych upraszcza zachowanie routingu ruchu dla wielu poddomen dla interfejsu API, aplikacji lub witryny sieci Web z tej samej reguły routingu bez konieczności modyfikowania konfiguracji w celu dodania i/lub określenia każdej poddomeny oddzielnie. Na przykład można zdefiniować `customer1.contoso.com`marszrutę `customer2.contoso.com`dla `customerN.contoso.com` programu , a używając tej samej reguły routingu, dodając domenę `*.contoso.com`wieloznaczną .

Oto niektóre z kluczowych scenariuszy, które zostały rozwiązane za pomocą obsługi domen wieloznacznych:

- Nie jest już potrzebne do dołączania każdej poddomeny w drzwiach frontowych, a następnie włączanie protokołu HTTPS do powiązania certyfikatu dla każdej poddomeny.
- Jeśli aplikacja doda nową poddomenę, nie jest już wymagana zmiana konfiguracji drzwi frontowych produkcji. Ponadto wcześniej wymagane było dodanie poddomeny, powiązanie certyfikatu z nim, dołączenie zasad zapory aplikacji sieci web (WAF), dodanie domeny do różnych reguł routingu.

> [!NOTE]
> Obecnie domeny wieloznaczne są obsługiwane tylko za pośrednictwem interfejsu API, programu PowerShell i interfejsu wiersza polecenia. Obsługa dodawania zarządzania domenami wieloznacznym za pośrednictwem witryny Azure portal nie jest dostępna.

## <a name="adding-wildcard-domains"></a>Dodawanie domen wieloznacznych

W sekcji Hosty lub Domeny można dołączać do domeny wieloznacznego. Podobnie jak poddomeny, drzwiami frontowymi sprawdza, czy istnieje również mapowanie CNAME dla domeny z symbolami wieloznacznych. To mapowanie DNS może być bezpośrednim `*.contoso.com` mapowaniem `contoso.azurefd.net` CNAME, takim jak `afdverify.contoso.com` mapowanie `afdverify.contoso.azurefd.net` tymczasowe afdverify lub za pośrednictwem tymczasowego mapowania afdverify, takiego jak mapowanie mapowane w celu sprawdzania poprawności mapy CNAME dla symboli wieloznacznych (usługa Azure DNS obsługuje rekordy symboli wieloznacznych).

Można również dodać dowolną liczbę poddomen jednopoziomowych domeny wieloznacznej w hostach frontendu, jeśli nie trafiają one na maks. limit hostów frontendu. Ta funkcja może być wymagana do definiowania innej trasy dla poddomeny niż pozostałe domeny (z domeny wieloznacznej) lub o innej zasadie WAF dla określonej poddomeny. Tak, `*.contoso.com` pozwoli `foo.contoso.com` dodanie bez konieczności ponownego udowodnienia własności domeny, ale `foo.bar.contoso.com` nie, `*.contoso.com`ponieważ nie jest to subdomena jednego poziomu . Aby `foo.bar.contoso.com` dodać bez dodatkowej `*.bar.contosonews.com` weryfikacji własności domeny, należy dodać.

### <a name="limitations"></a>Ograniczenia

1. Jeśli domena wieloznaczna jest dodawana w danym profilu drzwiami frontowymi, nie można dodać tego samego do żadnego innego profilu drzwi frontowych. 
2. Jeśli domena wieloznaczna jest dodawana w danym profilu drzwiami frontowymi, nie można dodać żadnych poddomen tej domeny wieloznacznej do innych drzwi ami frontowymi lub usługi Azure CDN z profilu firmy Microsoft
3. Jeśli poddomena domeny wieloznacznej jest dodawany do profilu drzwiach frontowych lub usługi Azure CDN z profilu firmy Microsoft, domena symboli wieloznacznych nie może zostać dodana do żadnego innego profilu drzwi frontowych. 
4. Jeśli dwa profile (drzwiami frontowymi lub usługa Azure CDN firmy Microsoft) mają różne poddomeny domeny głównej, nie można dodać domen wieloznacznych do żadnego z profili.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Powiązanie certyfikatów dla domen wieloznacznych i ich poddomen

Aby akceptować ruch HTTPS w domenie wieloznacznych, należy włączyć protokół HTTPS w domenie wieloznacznych. Powiązanie certyfikatu dla domeny wieloznacznych wymaga certyfikatu symboli wieloznacznych, czyli nazwa podmiotu certyfikatu powinna mieć również domenę symboli wieloznacznych.

> [!NOTE]
> Obecnie tylko przy użyciu własnej opcji certyfikatu SSL jest dostępna do włączania protokołu HTTPS dla domen wieloznacznych. Certyfikatów zarządzanych drzwiami frontowymi nie można używać w domenach wieloznacznych. 

Można użyć tego samego certyfikatu symboli wieloznacznych z magazynu kluczy dla poddomen lub obsługiwane jest również użycie certyfikatów zarządzanych przez drzwiami frontowymi dla poddomen.
Jeśli subdomena jest dodawana dla domeny wieloznacznych, a domena wieloznaczna miała już skojarzony certyfikat, nie można wyłączyć protokołu HTTPS dla tej poddomeny. Poddomena domyślnie użyje powiązania certyfikatu domeny wieloznacznego, chyba że zostanie zastąpiona przez inny certyfikat usługi Key Vault lub certyfikat zarządzany za pomocą usługi Front Door.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Zapora aplikacji sieci Web dla domen wieloznacznych i jej poddomen

Zasady WAF można dołączyć do domeny wieloznacznych podobnej do innych domen. Inne zasady WAF można zastosować do poddomeny domeny wieloznacznej. W przypadku poddomen należy jawnie określić zasady WAF, które mają być używane, a nawet jeśli jest to ta sama zasada co domena wieloznaczna. Subdomeny **nie** będą automatycznie dziedziczyć zasad WAF z domeny symboli wieloznacznych.

Jeśli masz scenariusz, w którym nie chcesz, aby WAF uruchamiał dla poddomeny, możesz utworzyć pustą zasadę WAF bez zarządzanych lub niestandardowych planów reguł.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Reguły routingu dla domen wieloznacznych i ich poddomen

Podczas konfigurowania reguły routingu można wybrać domenę wieloznaczną jako hosta frontendu. Można również mieć różne zachowanie trasy dla domeny wieloznacznych w porównaniu z poddomenami. Zgodnie z [opisem w jaki sposób drzwi frontowe dopasowywania tras,](front-door-route-matching.md)najbardziej szczegółowe dopasowanie dla domeny w różnych reguł routingu zostaną wybrane w czasie wykonywania.

> [!WARNING]
> Jeśli masz dwie reguły routingu, takie jak **Route 1:** `*.foo.com/*` `bar.foo.com/somePath/*` mapowane na backend pool A i `bar.foo.com/anotherPath/*` **Route 2:** mapowane do puli wewnętrznej bazy danych B i jeśli żądanie nadejdzie, klienci zobaczą błędy, ponieważ drzwi frontowe nie znajdą żadnego dopasowania na obu trasach. Dzieje się tak, ponieważ według naszego [algorytmu dopasowywania trasy](front-door-route-matching.md)drzwiami frontowymi wybiorą route 2 na podstawie bardziej szczegółowego dopasowania domeny, ale tylko po to, aby stwierdzić, że nie ma pasujących wzorców ścieżek. 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, jak [dodać domenę niestandardową w drzwiach frontowych](front-door-custom-domain.md).
- Dowiedz się, jak [włączyć protokół HTTPS w domenie niestandardowej](front-door-custom-domain-https.md).
