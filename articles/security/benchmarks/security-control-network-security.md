---
title: Kontrola zabezpieczeń platformy Azure — zabezpieczenia sieciowe
description: Zabezpieczenia sieci kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251876"
---
# <a name="security-control-network-security"></a>Kontrola bezpieczeństwa: Bezpieczeństwo sieci

Zalecenia dotyczące zabezpieczeń sieci koncentrują się na określaniu protokołów sieciowych, portów TCP/UDP i usług połączonych z siecią, które są dozwolone lub odmawiane dostępu do usług platformy Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Klient |

Upewnij się, że wszystkie wdrożenia podsieci sieci wirtualnej mają zastosowaną grupę zabezpieczeń sieci z kontrolkami dostępu do sieci specyficznymi dla zaufanych portów i źródeł aplikacji. Użyj usługi Azure Services z włączoną łączem prywatnym, wdrożyć usługę wewnątrz sieci wirtualnej lub połączyć się prywatnie przy użyciu prywatnych punktów końcowych. Aby zapoznać się z wymaganiami dotyczącymi specyficznych usług, zapoznaj się z zaleceniem zabezpieczeń dla tej konkretnej usługi.

Alternatywnie, jeśli masz określony przypadek użycia, wymagania mogą być spełnione przez implementowanie zapory platformy Azure.

Ogólne informacje na temat prywatnego linku:

https://docs.microsoft.com/azure/private-link/private-link-overview

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieć sieciową z konfiguracją zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak wdrożyć i skonfigurować Zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.2 | 9.3, 12.2 | Klient |

Skorzystaj z usługi Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby sieciowe na platformie Azure. Włącz dzienniki przepływu nsg i wysyłaj dzienniki do konta magazynu dla inspekcji ruchu.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Poznaj zabezpieczenia sieci udostępniane przez usługę Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.3 | 9.5 | Klient |

Wdrażanie zapory aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web w celu dodatkowego sprawdzania ruchu przychodzącego. Włącz ustawienie diagnostyczne dla waf i pozyskiwania logów do konta magazynu, Centrum zdarzeń lub obszaru roboczego analizy dzienników.

Jak wdrożyć usługę Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.4 | 12.3 | Klient |

Włącz standardową ochronę DDoS w sieciach wirtualnych platformy Azure, aby chronić się przed atakami DDoS. Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi adresami IP.

Wdrażanie zapory platformy Azure w każdej z granic sieci organizacji &quot;z włączoną analizą zagrożeń i skonfigurowane jako Alert i odmowa&quot; dla złośliwego ruchu sieciowego.

Użyj usługi Azure Security Center Just In Time Network dostęp do konfigurowania sieciowych grup zabezpieczeń, aby ograniczyć narażenie punktów końcowych na zatwierdzone adresy IP przez ograniczony okres.

Użyj narzędzia Azure Security Center Adaptive Network Hardening, aby zalecić konfiguracje sieciowej grupy zabezpieczeń, które ograniczają porty i źródłowe odpowiedzi na stany IP na podstawie analizy rzeczywistego ruchu i zagrożeń.

Jak skonfigurować ochronę przed atakami DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Jak wdrożyć Zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Poznaj zintegrowaną analizę zagrożeń w centrum zabezpieczeń platformy Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Poznaj program platformy Azure Security Center Adaptacyjne wzmocnienie sieci:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Poznaj kontrolę dostępu do sieci w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.5 | 12.5, 15.8 | Klient |

Rejestrowanie przepływu nsg loguje się do konta magazynu w celu wygenerowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowej aktywności, włącz przechwytywanie pakietów Obserwatora sieci.

Jak włączyć dzienniki przepływu nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć funkcję Obserwatora sieciowego:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.6 | 12.6, 12.7 | Klient |

Wdrażanie zapory platformy Azure w każdej z granic sieci organizacji &quot;z włączoną analizą zagrożeń i skonfigurowane jako Alert i odmowa&quot; dla złośliwego ruchu sieciowego.

Jak wdrożyć Zaporę platformy Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alerty za pomocą Zapory platformy Azure:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.7 | 12.9, 12.10 | Klient |

Wdrażanie bramy aplikacji platformy Azure dla aplikacji sieci web z włączoną protokołem HTTPS/SSL dla zaufanych certyfikatów.

Jak wdrożyć bramę aplikacji:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Jak skonfigurować bramę aplikacji do używania protokołu HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Poznaj równoważenie obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.8 | 1.5 | Klient |

Znaczniki usługi sieci wirtualnej służy do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub Zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Zrozumienie i używanie tagów serwisowych:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.9 | 11.1 | Klient |

Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą usługi Azure Policy.

Można również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez artefakty kluczowe środowisko pakowania, takie jak szablony usługi Azure Resource Manager, formanty RBAC i zasady, w jednej definicji planu. Można zastosować plan do nowych subskrypcji i dostosować kontroli i zarządzania poprzez przechowywanie wersji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady zasad platformy Azure dla sieci:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Jak utworzyć plan platformy Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.1 | 11.2 | Klient |

Użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z bezpieczeństwem sieci i przepływem ruchu. W przypadku poszczególnych reguł &quot;sieciowej grupy danych sieciowych należy użyć pola Opis,&quot; aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł umożliwiających ruch do/z sieci.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć nsg z configiem zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 1.11 | 11.3 | Klient |

Użyj usługi Azure Policy, aby sprawdzić poprawność (i/lub skorygować) konfigurację zasobów sieciowych.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady zasad platformy Azure dla sieci:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [Rejestrowanie i monitorowanie](security-control-logging-monitoring.md)
