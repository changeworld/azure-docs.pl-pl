---
title: Usługi zabezpieczeń platformy Azure i technologie | Dokumentacja firmy Microsoft
description: Artykuł zawiera listę wyselekcjonowanych technologii i usług zabezpieczeń Azure.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2018
ms.author: barclayn
ms.openlocfilehash: e52cee2cb642de6e54270c597e6ed99f7162d0ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641462"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Zabezpieczenia usług i technologii dostępnych w systemie Azure

W naszych rozmów z klientami Azure aktualnych i przyszłych system często prosi "masz listę wszystkich usług związanych z zabezpieczeniami i technologie, które platforma Azure ma oferować?"

Ocena możliwości dostawcy usług chmury, warto te informacje. Dlatego firma Microsoft umieściła tej listy, które ułatwią rozpoczęcie pracy.

Wraz z upływem czasu tej listy zmieniać i powiększać, podobnie jak Azure. Upewnij się sprawdzić tę stronę na bieżąco na bieżąco na naszych usług związanych z zabezpieczeniami i technologii.

## <a name="general-azure-security"></a>Ogólne Azure zabezpieczeń
|Usługa|Opis|
|--------|--------|
|[Azure&nbsp;zabezpieczeń&nbsp;Center](../security-center/security-center-intro.md)| Rozwiązania ochrony obciążenia chmury, które umożliwia zarządzanie zabezpieczeniami i advanced threat protection między obciążeń chmury hybrydowej.|
|[Usługa Azure Key Vault](../key-vault/key-vault-overview.md)| Magazynem kluczy tajnych bezpiecznego hasła, parametry połączenia i innych informacji potrzebnych do zachowania aplikacji działa. |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|Usługa monitorowania zbiera dane telemetryczne i innych danych, a także aparatu zapytań języka i analiza do świadczenia usługi operational insights dla aplikacji i zasobów. Mogą być używane autonomicznie lub z innych usług takich jak Centrum zabezpieczeń. |
|[Laboratoria Azure: tworzenie i testowanie](../devtest-lab/devtest-lab-overview.md)|Usługa, która pomaga deweloperom i testerom szybkie tworzenie środowisk podczas zminimalizować odpady i kontrolować koszty na platformie Azure.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpieczenia magazynu
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;magazynu&nbsp;usługi&nbsp;szyfrowania](../storage/common/storage-service-encryption.md)|Funkcja zabezpieczeń, która automatycznie szyfruje dane w magazynie Azure.   |
|[StorSimple zaszyfrowane magazynu hybrydowego](../storsimple/storsimple-ova-overview.md)| Rozwiązanie zintegrowanego magazynu, które zarządza zadań magazynu między urządzeniami lokalnymi i magazynu w chmurze Azure.|
|[Azure szyfrowania po stronie klienta](../storage/common/storage-client-side-encryption.md)| Rozwiązania szyfrowania po stronie klienta, które są szyfrowane dane w aplikacjach klienckich przed przekazaniem do usługi Azure Storage; podczas pobierania również odszyfrowuje dane. |
| [Sygnatury dostępu współdzielonego usługi Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Sygnatury dostępu współdzielonego umożliwiają dostęp delegowany do zasobów na koncie magazynu.  |
|[Klucze konta magazynu platformy Azure](../storage/common/storage-create-storage-account.md)| Metoda kontroli dostępu do magazynu Azure, który jest używany do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. |
|[Azure udziałów plików z szyfrowania protokołu SMB 3.0](../storage/files/storage-files-introduction.md)|To technologia zabezpieczeń sieci, która umożliwia automatyczne sieci szyfrowanie protokół udostępniania plików bloku komunikatów serwera (SMB). |
|[Usługa Azure Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| Rejestrowanie i generowanie metryk technologia danych na koncie magazynu. |

<!------>

## <a name="database-security"></a>Zabezpieczenia bazy danych
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;SQL&nbsp;zapory](../sql-database/sql-database-firewall-configure.md)|Funkcja kontroli dostępu do sieci, która chroni przed atakami sieciowymi w bazie danych. |
|[Azure&nbsp;SQL&nbsp;komórki&nbsp;poziomu szyfrowania](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologia zabezpieczeń bazy danych, która zapewnia szyfrowanie na poziomie szczegółowym.  |
| [Azure&nbsp;SQL&nbsp;szyfrowanie połączenia](../sql-database/sql-database-control-access.md)|Aby zapewnić bezpieczeństwo, usługa SQL Database kontroluje dostęp za pomocą reguł zapory, ograniczając adresy IP dla połączeń, stosując mechanizmy uwierzytelniania wymagające potwierdzenia tożsamości przez użytkowników oraz mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych. |
| [Azure SQL zawsze szyfrowania](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Ochrona poufnych danych, takich jak numery kart kredytowych lub numerów identyfikacyjnych national (na przykład USA numerów ubezpieczenia społecznego), przechowywane w bazach danych usługi Azure SQL Database lub SQL Server.  |
| [Azure&nbsp;SQL&nbsp;przezroczystego szyfrowania danych](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkcja zabezpieczeń bazy danych, który szyfruje magazyn całej bazy danych. |
| [Baza danych Azure SQL inspekcji](../sql-database/sql-database-auditing.md)|Baza danych inspekcji funkcja, która śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania na koncie magazynu Azure.  |


## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;roli&nbsp;na podstawie&nbsp;kontrola dostępu](../active-directory/role-based-access-control-configure.md)|Funkcja kontroli dostępu umożliwia użytkownikom uzyskiwanie dostępu do zasobów wymaganych do dostępu na podstawie ich ról w organizacji.  |
| [Azure Active Directory](../active-directory/active-directory-whatis.md)|Repozytorium uwierzytelniania opartego na chmurze, która obsługuje wiele tożsamości usługi zarządzania w systemie Azure i katalogu wielodostępnej, oparte na chmurze.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Usługa zarządzania tożsamości, który umożliwia kontrolę nad jak klienci rejestrację, logowanie i zarządzać nimi ich profile, przy użyciu aplikacji opartych na platformie Azure.   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| Wersja oparta na chmurze i zarządzanych usług domenowych w usłudze Active Directory. |
| [Uwierzytelnianie wieloskładnikowe platformy Azure](../active-directory/authentication/multi-factor-authentication.md)| Zapewnienie bezpieczeństwa, używającego kilka różnych metod uwierzytelniania i weryfikacji przed zezwoleniem na dostęp do zabezpieczonych informacji. |

## <a name="backup-and-disaster-recovery"></a>Kopia zapasowa i odzyskiwanie po awarii
|Usługa|Opis|
|------|--------|
| [Azure&nbsp;kopii zapasowej](../backup/backup-introduction-to-azure-backup.md)| Bazujących na platformie Azure Usługa używana do kopii zapasowej i przywracania danych w chmurze Azure. |
| [Azure&nbsp;lokacji&nbsp;odzyskiwania](../site-recovery/site-recovery-overview.md)|Usługa online, która replikuje obciążeń uruchomionych na fizycznych i maszyn wirtualnych (VM) z lokacji podstawowej do dodatkowej lokalizacji, aby włączyć usługi odzyskiwania po awarii. |

## <a name="networking"></a>Networking
|Usługa|Opis|
|------|--------|
| [Sieci&nbsp;zabezpieczeń&nbsp;grup](../virtual-network/virtual-networks-nsg.md)| Funkcja kontroli dostępu opartej na sieci przy użyciu krotka 5, aby zezwolić lub odmówić decyzji.  |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Urządzenie sieciowe używany jako punkt końcowy sieci VPN, aby umożliwić między lokalizacjami dostępu do sieci wirtualnych Azure.  |
| [Brama aplikacji Azure](../application-gateway/application-gateway-introduction.md)|Aplikacja sieci web zaawansowane załadować równoważenia, który można kierować na podstawie adresu URL i wykonać odciążanie protokołu SSL. |
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|TCP/UDP aplikacji sieciowej usługi równoważenia obciążenia. |
| [Usługa Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Dedykowane łącze sieci WAN między sieciami lokalnymi i sieci wirtualnych Azure. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| Globalne DNS modułu równoważenia obciążenia.|
| [Serwer Proxy aplikacji Azure](../active-directory/active-directory-application-proxy-get-started.md)| Uwierzytelnianie frontonu używany do zabezpieczania dostępu zdalnego dla aplikacji sieci web hostowane lokalnie. |