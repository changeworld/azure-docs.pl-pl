---
title: Omówienie zasad zapory aplikacji sieci Web platformy Azure (WAF)
description: Ten artykuł jest omówieniem zasad globalnej zapory aplikacji sieci Web (WAF), na lokację i na identyfikator URI.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060269"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Omówienie zasad zapory aplikacji sieci Web platformy Azure (WAF)

Zasady zapory aplikacji sieci Web zawierają wszystkie ustawienia i konfiguracje WAF. Obejmuje to wykluczenia, reguły niestandardowe, reguły zarządzane i tak dalej. Te zasady są następnie skojarzone z bramą aplikacji (globalną), odbiornikiem (dla witryny) lub regułą opartą na ścieżce (na identyfikator URI), aby weszły w życie.

> [!NOTE]
> Azure Web Application Firewall (WAF) na lokację i na URI zasady są w publicznej wersji zapoznawczej.
> 
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nie ma ograniczeń co do liczby zasad, które można utworzyć. Podczas tworzenia zasad, musi być skojarzony z bramą aplikacji, aby wejść w życie. Może być skojarzony z dowolną kombinacją bram aplikacji, odbiorników i reguł opartych na ścieżce.

## <a name="global-waf-policy"></a>Globalna polityka WAF

Po skojarzeniu zasad WAF globalnie każda lokacja za bramą aplikacji WAF jest chroniony za pomocą tych samych reguł zarządzanych, reguł niestandardowych, wykluczeń i innych skonfigurowanych ustawień.

Jeśli chcesz, aby jedna zasada miała być stosowana do wszystkich witryn, możesz skojarzyć zasady z bramą aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasad zapory aplikacji sieci Web dla bramy aplikacji](create-waf-policy-ag.md) w celu utworzenia i zastosowania zasad WAF przy użyciu portalu Azure. 

## <a name="per-site-waf-policy"></a>Zasady WAF dla witryny

Za pomocą zasad WAF dla poszczególnych witryn można chronić wiele witryn o różnych potrzebach zabezpieczeń za jednym WAF przy użyciu zasad dla poszczególnych witryn. Na przykład jeśli za siecią WAF znajduje się pięć lokacji, można mieć pięć oddzielnych zasad WAF (po jednej dla każdego odbiornika), aby dostosować wykluczenia, reguły niestandardowe, zestawy reguł zarządzanych i wszystkie inne ustawienia WAF dla każdej witryny.

Załóżmy, że brama aplikacji ma zastosowaną zasadę globalną. Następnie należy zastosować inne zasady do odbiornika na tej bramie aplikacji. Zasady odbiornika teraz staje się skuteczne tylko dla tego odbiornika. Zasady globalne bramy aplikacji nadal mają zastosowanie do wszystkich innych odbiorników i reguł opartych na ścieżce, które nie mają przypisanej określonej zasady.

## <a name="per-uri-policy"></a>Zasady dotyczące identyfikatoraURI

Aby uzyskać jeszcze więcej dostosowywania do poziomu identyfikatora URI, można skojarzyć zasady WAF z regułą opartą na ścieżce. Jeśli istnieją pewne strony w jednej witrynie, które wymagają różnych zasad, można wprowadzić zmiany w zasadach WAF, które mają wpływ tylko na dany identyfikator URI. Może to dotyczyć strony płatności lub logowania lub innych identyfikatorów URI, które wymagają jeszcze bardziej szczegółowych zasad WAF niż inne witryny stojące za WAF.

Podobnie jak w poszczególnych zasadach WAF witryny, bardziej szczegółowe zasady zastępują mniej szczegółowe. Oznacza to, że zasady na identyfikator URI na mapie ścieżki adresu URL zastępują wszystkie zasady WAF dla witryny lub globalnej nad nią.

## <a name="example"></a>Przykład

Załóżmy, że masz trzy witryny: contoso.com, fabrikam.com i adatum.com wszystkie za tą samą bramą aplikacji. Chcesz, aby WAF był stosowany do wszystkich trzech witryn, ale potrzebujesz dodatkowego zabezpieczenia w adatum.com, ponieważ to jest miejsce, w którym klienci odwiedzają, przeglądaj i kupują produkty.

Zasady globalne można zastosować do WAF z pewnymi podstawowymi ustawieniami, wykluczeniami lub regułami niestandardowymi, jeśli to konieczne, aby uniemożliwić blokowanie ruchu przez niektóre fałszywe alarmy. W takim przypadku nie ma potrzeby, aby globalne reguły iniekcji SQL uruchomione, ponieważ fabrikam.com i contoso.com są statyczne strony bez zaplecza SQL. Możesz więc wyłączyć te reguły w zasadach globalnych.

Ta globalna polityka jest odpowiednia dla contoso.com i fabrikam.com, ale musisz być bardziej ostrożny z adatum.com, w których obsługiwane są informacje logowania i płatności. Można zastosować zasady dla witryny do odbiornika adatum i pozostawić reguły SQL uruchomione. Załóżmy również, że istnieje plik cookie blokujący ruch, więc możesz utworzyć wykluczenie dla tego pliku cookie, aby zatrzymać fałszywie dodatni. 

Identyfikator URI adatum.com/payments jest miejscem, w którym należy zachować ostrożność. Zastosuj inne zasady dotyczące tego identyfikatora URI i pozostaw wszystkie reguły włączone, a także usuń wszystkie wykluczenia.

W tym przykładzie masz zasady globalne, które mają zastosowanie do dwóch lokacji. Masz zasady dla poszczególnych witryn, które mają zastosowanie do jednej lokacji, a następnie zasady dla identyfikatorów URI, która ma zastosowanie do jednej określonej reguły opartej na ścieżce. Zobacz (wstawić łącze tutaj, gdy istnieje) instrukcje tworzenia zasad dla witryny i identyfikatora URI dla odpowiedniego programu PowerShell w tym przykładzie.

## <a name="existing-waf-configurations"></a>Istniejące konfiguracje WAF

Wszystkie nowe ustawienia WAF zapory aplikacji sieci Web (reguły niestandardowe, konfiguracje zestawu reguł zarządzanych, wykluczenia itd.) istnieją w zasadach WAF. Jeśli masz istniejący WAF, te ustawienia mogą nadal istnieć w konfiguracji WAF. Aby uzyskać więcej informacji na temat przechodzenia do nowej polityki WAF, [migruj w uchodź w uchylić w UAF.](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy) 


## <a name="next-steps"></a>Następne kroki

Tworzenie zasad dla witryny i identyfikatorów URI przy użyciu programu Azure PowerShell.
