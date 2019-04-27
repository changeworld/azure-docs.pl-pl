---
title: Usługi i technologie zabezpieczeń platformy Azure | Dokumentacja firmy Microsoft
description: Artykuł zawiera listę nadzorowanych usługi i technologie zabezpieczeń platformy Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 13183282e5e607f0052194a474203f97e0160adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610909"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Zabezpieczenia usługi i technologie dostępne na platformie Azure

Biorący udział w dyskusjach bieżących i przyszłych klientów platformy Azure firma Microsoft często się prośba o "masz listę wszystkich usług związanych z zabezpieczeniami i technologii, które platforma Azure ma do zaoferowania?"

Opcje dostawcy usługi w chmurze możesz ocenić, warto te informacje. Dlatego udostępniliśmy tej listy, aby rozpocząć pracę.

Wraz z upływem czasu ta lista zmiany i rozwój tak, jak platforma Azure zrobi. Upewnij się sprawdzić tę stronę w regularnych odstępach czasu, aby najnowsze informacje na temat naszych usług związanych z zabezpieczeniami i technologii.

## <a name="general-azure-security"></a>Zabezpieczenia platformy Azure ogólne
|Usługa|Opis|
|--------|--------|
|[Azure&nbsp;zabezpieczeń&nbsp;Centrum](../security-center/security-center-intro.md)| Rozwiązanie ochrony obciążenia chmury, które zapewnia zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w obciążeniach chmury hybrydowej.|
|[Usługa Azure Key Vault](../key-vault/key-vault-overview.md)| Bezpieczny magazyn wpisów tajnych dla hasła, parametry połączenia i inne informacje, które musisz zachować aplikacji działa. |
|[Dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md)|Usługa monitorowania zbiera dane telemetryczne i inne dane, która zapewnia aparat zapytań języka i analizy, aby dostarczać usługi operational insights dla aplikacji i zasobów. Mogą być używane autonomicznie lub z innymi usługami takich jak usługa Security Center. |
|[Azure Dev/Test Labs](../devtest-lab/devtest-lab-overview.md)|To usługa, która pomaga programistom i testerom szybko twórz środowiska na platformie Azure, minimalizując równocześnie straty i kontrolując koszty.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpieczenia magazynu
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;magazynu&nbsp;usługi&nbsp;szyfrowania](../storage/common/storage-service-encryption.md)|Funkcja zabezpieczeń, która automatycznie szyfruje dane w usłudze Azure storage.   |
|[StorSimple szyfrowany magazyn hybrydowy](../storsimple/storsimple-ova-overview.md)| Zintegrowane rozwiązanie do magazynowania, który zarządza zadaniami magazynowania między urządzeniami lokalnymi i magazynem w chmurze Azure.|
|[Szyfrowanie po stronie klienta Azure](../storage/common/storage-client-side-encryption.md)| To rozwiązanie szyfrowania po stronie klienta, które szyfruje danych wewnątrz aplikacji klienckich przed przekazaniem do usługi Azure Storage; podczas pobierania, również odszyfrowuje dane. |
| [Sygnatury dostępu współdzielonego usługi Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu.  |
|[Klucze konta magazynu platformy Azure](../storage/common/storage-create-storage-account.md)| Metody kontroli dostępu dla usługi Azure storage, który jest używany do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. |
|[Udziały plików platformy Azure przy użyciu protokołu SMB 3.0 szyfrowania](../storage/files/storage-files-introduction.md)|Technologia zabezpieczeń sieci, która umożliwia automatyczne szyfrowanie protokół udostępniania plików bloku komunikatów serwera (SMB) w sieci. |
|[Analityka usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/Storage-Analytics)| Technologia rejestrowania i metryk, generowania danych na koncie magazynu. |

<!------>

## <a name="database-security"></a>Zabezpieczenia bazy danych
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](../sql-database/sql-database-firewall-configure.md)|Funkcja kontroli dostępu do sieci, która chroni przed atakami sieciowymi wykorzystującymi do bazy danych. |
|[Azure&nbsp;SQL&nbsp;Cell&nbsp;Level Encryption](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologia zabezpieczeń bazy danych, która zapewnia szyfrowanie na poziomie szczegółowym.  |
| [Azure&nbsp;SQL&nbsp;Connection Encryption](../sql-database/sql-database-control-access.md)|Aby zapewnić bezpieczeństwo, usługa SQL Database kontroluje dostęp za pomocą reguł zapory, ograniczając adresy IP dla połączeń, stosując mechanizmy uwierzytelniania wymagające potwierdzenia tożsamości przez użytkowników oraz mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych. |
| [Usługi Azure SQL zawsze szyfrowania](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Ochrona poufnych danych, takich jak numery kart kredytowych czy narodowe numery identyfikacyjne (na przykład USA numery ubezpieczenia społecznego), przechowywane w bazach danych Azure SQL Database lub SQL Server.  |
| [Azure&nbsp;SQL&nbsp;Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkcja zabezpieczeń bazy danych, która szyfruje magazyn całą bazę danych. |
| [Usługa Azure SQL Database inspekcji](../sql-database/sql-database-auditing.md)|Baza danych inspekcji funkcji, która śledzi zdarzenia bazy danych i zapisuje je do inspekcji logowania na koncie magazynu platformy Azure.  |


## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;roli&nbsp;na podstawie&nbsp;kontroli dostępu](../active-directory/role-based-access-control-configure.md)|Funkcja kontroli dostępu umożliwia użytkownikom dostęp do zasobów, które są wymagane do dostępu na podstawie ich ról w organizacji.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|Repozytorium uwierzytelniania opartego na chmurze, która obsługuje wiele usług zarządzania tożsamościami w ramach platformy Azure i katalogu wielodostępna, oparta na chmurze.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Usługa zarządzania tożsamościami, która umożliwia kontrolę nad jak klienci rejestracji, logowania i zarządzają swoimi profilami podczas korzystania z aplikacji opartych na platformie Azure.   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| Oparte na chmurze i zarządzana wersja Active Directory Domain Services. |
| [Usługa Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| Zapewnienie bezpieczeństwa, korzystającej z kilku różnych formy uwierzytelniania i weryfikacji przed zezwoleniem na dostęp do zabezpieczonych informacji. |

## <a name="backup-and-disaster-recovery"></a>Kopia zapasowa i odzyskiwanie po awarii
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;kopii zapasowej](../backup/backup-introduction-to-azure-backup.md)| Oparte na platformie Azure usługa umożliwia tworzenie kopii zapasowej i przywracanie danych w chmurze platformy Azure. |
| [Azure&nbsp;Site&nbsp;Recovery](../site-recovery/site-recovery-overview.md)|Usługa online, która replikuje obciążenia uruchomione na fizycznych i maszyn wirtualnych (VM) z lokacji głównej do lokalizacji dodatkowej, aby włączyć odzyskiwanie usług w po awarii. |

## <a name="networking"></a>Networking
|Usługa|Opis|
|------|--------|
| [Sieć&nbsp;zabezpieczeń&nbsp;grup](../virtual-network/virtual-networks-nsg.md)| Funkcja kontroli dostępu opartej na sieci korzystania z krotki 5 w celu podejmowania blokują lub zezwalają na decyzje.  |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Urządzenie sieciowe używane jako obejmujących punktu końcowego sieci VPN, aby zezwolić na dostęp do sieci wirtualnych platformy Azure.  |
| [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)|Aplikacja sieci web zaawansowane równoważenia który trasy, na podstawie adresu URL i wykonaj odciążanie protokołu SSL. |
|[Zapora aplikacji sieci Web](../application-gateway/waf-overview.md) (WAF)|Funkcja Application Gateway, która zapewnia scentralizowaną ochronę aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach|
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|TCP/UDP aplikacji moduły równoważenia obciążenia sieciowego. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Dedykowane łącze sieci WAN między sieciami lokalnymi i sieciami wirtualnymi platformy Azure. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| Moduł równoważenia obciążenia globalne DNS.|
| [Serwer Proxy aplikacji platformy Azure](../active-directory/active-directory-application-proxy-get-started.md)| Uwierzytelnianie frontonu umożliwia bezpieczny dostęp zdalny dla aplikacji sieci web hostowanych lokalnie. |
|[Zaporę platformy Azure](../firewall/overview.md)|Usługa zabezpieczeń sieci zarządzanej, oparte na chmurze, która chroni Twoje zasoby usługi Azure Virtual Network.|
|[Usługa Azure DDoS protection](../virtual-network/ddos-protection-overview.md)|W połączeniu z najlepsze rozwiązania dotyczące projektowania aplikacji, zapewnia ochronę przed atakami DDoS.|
|[Punkty końcowe usługi dla sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md)|Rozszerza Twojej sieci wirtualnej prywatną przestrzeń adresową oraz tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia.|