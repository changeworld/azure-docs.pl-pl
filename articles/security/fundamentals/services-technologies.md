---
title: Usługi i technologie zabezpieczeń platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera wyselekcjonowaną listę usług i technologii usługi Azure Security.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 741e6b9cad20645fdfc085623fd9492a12ac1dfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726563"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Usługi zabezpieczeń i technologie dostępne na platformie Azure

W naszych dyskusjach z obecnymi i przyszłymi klientami platformy Azure często jesteśmy pytani "czy masz listę wszystkich usług i technologii związanych z zabezpieczeniami, które platforma Azure ma do zaoferowania?".

Podczas oceny opcji dostawcy usług w chmurze warto mieć te informacje. Dlatego udostępniliśmy tę listę, aby rozpocząć.

Z biegiem czasu ta lista będzie się zmieniać i rozwijać, tak jak platforma Azure. Pamiętaj, aby regularnie sprawdzać tę stronę, aby być na bieżąco z naszymi usługami i technologiami związanymi z bezpieczeństwem.

## <a name="general-azure-security"></a>Ogólne zabezpieczenia platformy Azure
|Usługa|Opis|
|--------|--------|
|[Usługa&nbsp;&nbsp;Azure Security Center](/azure/security-center/security-center-intro)| Rozwiązanie do ochrony obciążenia w chmurze, które zapewnia zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w przypadku obciążeń chmury hybrydowej.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Bezpieczne wpisy tajne dla haseł, ciągów połączeń i innych informacji potrzebnych do utrzymania działania aplikacji. |
|[Dzienniki usługi Azure Monitor](/azure/log-analytics/log-analytics-overview)|Usługa monitorowania, która zbiera dane telemetryczne i inne dane oraz udostępnia język zapytań i aparat analizy w celu dostarczania szczegółowych informacji operacyjnych dotyczących aplikacji i zasobów. Może być używany samodzielnie lub z innymi usługami, takimi jak Centrum zabezpieczeń. |
|[Laboratoria deweloperów/testów platformy Azure](/azure/lab-services/devtest-lab-overview)|Usługa, która pomaga deweloperom i testerom szybko tworzyć środowiska na platformie Azure, minimalizując jednocześnie odpady i kontrolując koszty.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpieczenia magazynu
|Usługa|Opis|
|------|--------|
| [Szyfrowanie&nbsp;&nbsp;usługi Azure&nbsp;Storage](/azure/storage/common/storage-service-encryption)|Funkcja zabezpieczeń, która automatycznie szyfruje dane w magazynie platformy Azure.   |
|[Szyfrowany hybrydowy magazyn storsimple](/azure/storsimple/storsimple-ova-overview)| Zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynowania między urządzeniami lokalnymi a magazynem w chmurze platformy Azure.|
|[Szyfrowanie po stronie klienta platformy Azure](/azure/storage/common/storage-client-side-encryption)| Rozwiązanie szyfrowania po stronie klienta, które szyfruje dane wewnątrz aplikacji klienckich przed przekazaniem do usługi Azure Storage; również odszyfrowuje dane podczas pobierania. |
| [Podpisy dostępu współdzielonego usługi Azure Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Podpis dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu.  |
|[Klucze kont usługi Azure Storage](/azure/storage/common/storage-create-storage-account)| Metoda kontroli dostępu dla magazynu platformy Azure, która jest używana do uwierzytelniania, gdy jest uzyskiwać dostęp do konta magazynu. |
|[Udziały w pliku platformy Azure za pomocą szyfrowania SMB 3.0](/azure/storage/files/storage-files-introduction)|Technologia zabezpieczeń sieciowych, która umożliwia automatyczne szyfrowanie sieci dla protokołu udostępniania plików bloku komunikatów serwera (SMB). |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| Technologia rejestrowania i generowania danych na koncie magazynu. |

<!------>

## <a name="database-security"></a>Zabezpieczenia bazy danych
|Usługa|Opis|
|------|--------|
| [Zapora&nbsp;SQL platformy Azure&nbsp;](/azure/sql-database/sql-database-firewall-configure)|Funkcja kontroli dostępu do sieci, która chroni przed atakami sieciowymi do bazy danych. |
|[Szyfrowanie&nbsp;&nbsp;poziomu komórek SQL platformy Azure&nbsp;](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologia zabezpieczeń bazy danych, która zapewnia szyfrowanie na poziomie szczegółowym.  |
| [Szyfrowanie&nbsp;połączenia SQL platformy Azure&nbsp;](/azure/sql-database/sql-database-control-access)|Aby zapewnić bezpieczeństwo, usługa SQL Database kontroluje dostęp za pomocą reguł zapory, ograniczając adresy IP dla połączeń, stosując mechanizmy uwierzytelniania wymagające potwierdzenia tożsamości przez użytkowników oraz mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych. |
| [Szyfrowanie sql azure zawsze](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Chroni poufne dane, takie jak numery kart kredytowych lub krajowe numery identyfikacyjne (na przykład numery ubezpieczenia społecznego USA), przechowywane w bazach danych usługi Azure SQL Database lub SQL Server.  |
| [Szyfrowanie przezroczystych danych sql platformy Azure&nbsp;&nbsp;](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkcja zabezpieczeń bazy danych, która szyfruje magazyn całej bazy danych. |
| [Inspekcja bazy danych SQL platformy Azure](/azure/sql-database/sql-database-auditing)|Funkcja inspekcji bazy danych, która śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure.  |


## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem
|Usługa|Opis|
|------|--------|
| [Kontrola&nbsp;&nbsp;dostępu&nbsp;oparta na rolach platformy Azure](/azure/active-directory/role-based-access-control-configure)|Funkcja kontroli dostępu zaprojektowana w celu umożliwienia użytkownikom dostępu tylko do zasobów, do których są zobowiązani, na podstawie ich ról w organizacji.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Oparte na chmurze repozytorium uwierzytelniania, które obsługuje katalog wielodostępny, oparty na chmurze i wiele usług zarządzania tożsamościami na platformie Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Usługa zarządzania tożsamościami, która umożliwia kontrolę nad tym, jak klienci się logują, logują się i zarządzają swoimi profilami podczas korzystania z aplikacji opartych na platformie Azure.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Oparta na chmurze i zarządzana wersja Usług domenowych Active Directory. |
| [Uwierzytelnianie wieloskładnikowe platformy Azure](/azure/active-directory/authentication/multi-factor-authentication)| Przepis zabezpieczeń, który wykorzystuje kilka różnych form uwierzytelniania i weryfikacji przed zezwoleniem na dostęp do zabezpieczonych informacji. |

## <a name="backup-and-disaster-recovery"></a>Odzyskiwanie kopii zapasowych i odzyskiwania po awarii
|Usługa|Opis|
|------|--------|
| [Kopia&nbsp;zapasowa platformy Azure](/azure/backup/backup-introduction-to-azure-backup)| Usługa oparta na platformie Azure używana do krotnego utworzenia kopii zapasowej i przywracania danych w chmurze platformy Azure. |
| [Odzyskiwanie&nbsp;&nbsp;witryny platformy Azure](/azure/site-recovery/site-recovery-overview)|Usługa online, która replikuje obciążenia uruchomione na maszynach fizycznych i wirtualnych (VM) z lokacji głównej do lokalizacji dodatkowej, aby umożliwić odzyskiwanie usług po awarii. |

## <a name="networking"></a>Obsługa sieci
|Usługa|Opis|
|------|--------|
| [&nbsp;Sieciowe&nbsp;grupy zabezpieczeń](/azure/virtual-network/virtual-networks-nsg)| Funkcja kontroli dostępu sieciowego przy użyciu 5-krotki do podejmowania decyzji zezwalania lub odrzucania.  |
| [Brama sieci VPN platformy Azure](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Urządzenie sieciowe używane jako punkt końcowy sieci VPN w celu umożliwienia dostępu międzylokalizanego do sieci wirtualnych platformy Azure.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|Zaawansowany moduł równoważenia obciążenia aplikacji sieci web, który może kierować na podstawie adresu URL i wykonywać odciążanie SSL. |
|[Zapora aplikacji sieci Web](/azure/frontdoor/waf-overview) (WAF)|Funkcja bramy aplikacji zapewniająca scentralizowaną ochronę aplikacji internetowych przed typowymi exploitami i lukami w zabezpieczeniach|
| [Moduł równoważenia obciążenia platformy Azure](/azure/load-balancer/load-balancer-overview)|Moduł równoważenia obciążenia sieciowego aplikacji TCP/UDP. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Dedykowane łącze sieci WAN między sieciami lokalnymi a sieciami wirtualnymi platformy Azure. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Globalny moduł równoważenia obciążenia DNS.|
| [Serwer proxy aplikacji platformy Azure](/azure/active-directory/active-directory-application-proxy-get-started)| Uwierzytelniający front-end używany do zabezpieczania dostępu zdalnego dla aplikacji internetowych hostowanych lokalnie. |
|[Azure Firewall](/azure/firewall/overview)|Zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure.|
|[Ochrona DDoS platformy Azure](/azure/virtual-network/ddos-protection-overview)|W połączeniu z najlepszymi rozwiązaniami w zakresie projektowania aplikacji zapewnia obronę przed atakami DDoS.|
|[Punkty końcowe usługi sieci wirtualnej](/azure/virtual-network/virtual-network-service-endpoints-overview)|Rozszerza prywatną przestrzeń adresową sieci wirtualnej i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia.|