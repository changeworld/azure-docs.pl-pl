---
title: Kontrola zabezpieczeń Azure — Zabezpieczenia sieci
description: Zabezpieczenia sieci kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 376d7c1a9d2fe2ebce857362fd216e2047eb1f7b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934333"
---
# <a name="security-control-network-security"></a>Kontrola zabezpieczeń: zabezpieczenia sieci

Zalecenia dotyczące zabezpieczeń sieci koncentrują się na określaniu, które protokoły sieciowe, porty TCP/UDP i usługi połączone z siecią są dozwolone lub odrzucane dostępu do usług platformy Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1-14.3 | Klient |

Upewnij się, że wszystkie wdrożenia podsieci Virtual Network mają grupę zabezpieczeń sieci stosowaną z kontrolami dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji. Korzystaj z usług platformy Azure z włączonym linkiem prywatnym, Wdrażaj usługę w sieci wirtualnej lub łącz się prywatnie przy użyciu prywatnych punktów końcowych. Wymagania dotyczące konkretnych usług zapoznaj się z zaleceniami dotyczącymi zabezpieczeń tej konkretnej usługi.

Alternatywnie, jeśli masz określony przypadek użycia, wymagania mogą być spełnione przez zaimplementowanie zapory platformy Azure.

Informacje ogólne o łączu prywatnym:

https://docs.microsoft.com/azure/private-link/private-link-overview

Jak utworzyć Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak wdrożyć i skonfigurować zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.2 | 9,3, 12,2 | Klient |

Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych na platformie Azure. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.3 | 9.5 | Klient |

Wdróż zaporę aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Włącz ustawienie diagnostyczne dla WAF i pobierania dzienników do konta magazynu, centrum zdarzeń lub Log Analytics obszaru roboczego.

Jak wdrożyć usługę Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.4 | 12,3 | Klient |

Włącz ochronę standardową DDoS w sieciach wirtualnych platformy Azure, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi adresami IP.

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji z włączoną analizą zagrożeń i skonfigurowaną do &quot;alertu i Odmów&quot; w przypadku złośliwego ruchu sieciowego.

Użyj Azure Security Center dostęp do sieci w czasie w celu skonfigurowania sieciowych grup zabezpieczeń w celu ograniczenia narażenia punktów końcowych na zatwierdzone adresy IP przez ograniczony okres.

Aby zalecać sieciowej grupy zabezpieczeń konfiguracje, które ograniczają porty i źródłowe adresy IP w oparciu o rzeczywisty ruch i analizę zagrożeń, należy użyć Azure Security Center.

Jak skonfigurować ochronę DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Jak wdrożyć zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Opis Azure Security Center zintegrowanej analizy zagrożeń:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Informacje o tym, Azure Security Center Access Control sieci w czasie:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.5 | 12,5, 15,8 | Klient |

Zarejestruj dzienniki przepływu sieciowej grupy zabezpieczeń na koncie magazynu w celu wygenerowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowego działania, Włącz funkcję przechwytywania pakietów Network Watcher.

Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.6 | 12,6, 12,7 | Klient |

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji z włączoną analizą zagrożeń i skonfigurowaną do &quot;alertu i Odmów&quot; w przypadku złośliwego ruchu sieciowego.

Jak wdrożyć zaporę platformy Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alerty za pomocą zapory platformy Azure: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.7 | 12,9, 12,10 | Klient |

Wdróż Application Gateway platformy Azure dla aplikacji sieci Web z włączonym protokołem HTTPS/SSL dla zaufanych certyfikatów.

Jak wdrożyć Application Gateway:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Informacje na temat równoważenia obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.8 | 1.5 | Klient |

Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Zrozumienie i używanie tagów usługi:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.9 | 11,1 | Klient |

Zdefiniuj i Implementuj standardowe konfiguracje zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy.

Możesz również użyć planów platformy Azure, aby uprościć duże wdrożenia platformy Azure przez spakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrolki RBAC i zasady, w ramach jednej definicji planu. Możesz zastosować plan do nowych subskrypcji i dostosować kontrolę i zarządzanie przy użyciu wersji.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady Azure Policy dla sieci:

https://docs.microsoft.com/azure/governance/policy/samples/#network

Jak utworzyć Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1.1 | 11.2 | Klient |

Używaj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola &quot;opis&quot;, aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 1,11 | 11,3 | Klient |

Użyj Azure Policy, aby sprawdzić poprawność konfiguracji (i/lub skorygować) dla zasobów sieciowych.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady Azure Policy dla sieci:

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [Rejestrowanie i monitorowanie](security-control-logging-monitoring.md)