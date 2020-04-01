---
title: Omówienie funkcji zabezpieczeń
description: Dowiedz się więcej o funkcjach zabezpieczeń w usłudze Azure Backup, które pomagają chronić dane kopii zapasowej i spełniać potrzeby firmy w zakresie bezpieczeństwa.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 91a0f29862b0c9c35e562c143e28ebbc6c39cf94
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423186"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Omówienie funkcji zabezpieczeń w usłudze Azure Backup

Jednym z najważniejszych kroków, które można podjąć w celu ochrony danych jest niezawodna infrastruktura tworzenia kopii zapasowych. Ale równie ważne jest zapewnienie, że dane są archiwizowane w bezpieczny sposób i że kopie zapasowe są chronione przez cały czas. Usługa Azure Backup zapewnia bezpieczeństwo środowiska kopii zapasowej — zarówno podczas przesyłania danych, jak i w stanie spoczynku. W tym artykule wymieniono funkcje zabezpieczeń w usłudze Azure Backup, które pomagają chronić dane kopii zapasowej i spełniać potrzeby firmy w zakresie zabezpieczeń.

## <a name="management-and-control-of-identity-and-user-access"></a>Zarządzanie tożsamością i dostępem użytkowników i kontrola ich kontroli

Usługa Azure Backup umożliwia zarządzanie dostępem szczegółowym przy użyciu [kontroli dostępu opartej na rolach platformy Azure (RBAC).](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) RBAC pozwala na segregować obowiązki w zespole i przyznać tylko ilość dostępu do użytkowników niezbędnych do wykonywania ich pracy.

Usługa Azure Backup udostępnia trzy wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi:

* Współautor tworzenia kopii zapasowych — do tworzenia kopii zapasowych i zarządzania nimi, z wyjątkiem usuwania magazynu usług odzyskiwania i udzielania dostępu innym osobom
* Operator kopii zapasowych — wszystko, co robi współautor, z wyjątkiem usuwania kopii zapasowych i zarządzania zasadami tworzenia kopii zapasowych
* Backup Reader - uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi

Dowiedz się więcej o [kontroli dostępu opartej na rolach w celu zarządzania pomocą w kopii zapasowej platformy Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Usługa Azure Backup ma kilka zabezpieczeń wbudowanych w usługę, aby zapobiegać lukom w zabezpieczeniach, wykrywać i reagować na nie. Dowiedz się więcej o [kontrolach zabezpieczeń usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separacja między magazynem gościa a magazynem platformy Azure

Dzięki usłudze Azure Backup, która obejmuje tworzenie kopii zapasowych maszyn wirtualnych oraz program SQL i SAP HANA w kopii zapasowej maszyny Wirtualnej, dane kopii zapasowej są przechowywane w magazynie platformy Azure, a gość nie ma bezpośredniego dostępu do magazynu kopii zapasowych ani jego zawartości.  Dzięki kopii zapasowej maszyny wirtualnej tworzenie i magazyn migawki kopii zapasowej odbywa się przez sieć szkieletową platformy Azure, w której gość nie ma udziału innego niż tworzenie obciążenia dla spójnych kopii zapasowych aplikacji.  Z SQL i SAP HANA rozszerzenie kopii zapasowej pobiera tymczasowy dostęp do zapisu do określonych obiektów blob.  W ten sposób, nawet w środowisku zagrożonym, istniejące kopie zapasowe nie mogą być modyfikowane lub usuwane przez gościa.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Łączność z Internetem nie jest wymagana dla tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure

Kopia zapasowa maszyn wirtualnych platformy Azure wymaga przenoszenia danych z dysku maszyny wirtualnej do magazynu usług odzyskiwania. Jednak wszystkie wymagane komunikacji i transferu danych odbywa się tylko w sieci szkieletowej platformy Azure bez konieczności dostępu do sieci wirtualnej. W związku z tym tworzenie kopii zapasowych maszyn wirtualnych platformy Azure umieszczonych wewnątrz zabezpieczonych sieci nie wymaga zezwalania na dostęp do dowolnych adresów IP lub FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Prywatne punkty końcowe dla kopii zapasowej platformy Azure

Teraz można użyć [prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) do bezpiecznego tworzenia kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu usług odzyskiwania. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla magazynu, więc nie trzeba udostępniać sieci wirtualnych na żadnych publicznych adresów IP. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które są uruchamiane wewnątrz maszyn wirtualnych platformy Azure. Może być również używany dla serwerów lokalnych przy użyciu agenta MARS.

>[!NOTE]
> Ta funkcja jest obecnie w ograniczonej dostępności. Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) i [wyślij do nas wiadomość e-mail,](mailto:azbackupnetsec@microsoft.com) jeśli jesteś zainteresowany korzystaniem z prywatnych punktów końcowych dla usługi Azure Backup. Możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Szyfrowanie danych w tranzycie i w stanie spoczynku

Szyfrowanie chroni dane i pomaga spełnić zobowiązania dotyczące zabezpieczeń i zgodności z przepisami organizacji. Na platformie Azure dane przesyłane między magazynem platformy Azure a magazynem są chronione przez protokół HTTPS. Te dane pozostają w sieci szkieletowej platformy Azure.

* Dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Można również zaszyfrować maszyny wirtualne z kopii zapasowej dysku w magazynie usług odzyskiwania przy użyciu [kluczy zarządzanych klientów](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) przechowywanych w magazynie azure key vault. Aby włączyć to szyfrowanie, nie trzeba podejmować żadnych jawnych działań. Dotyczy wszystkich obciążeń, które są archiwizowane w magazynie usług odzyskiwania.

* Usługa Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn wirtualnych platformy Azure, które mają swoje dyski systemu operacyjnego/danych zaszyfrowane za pomocą szyfrowania dysków azure (ADE). [Dowiedz się więcej o zaszyfrowanych maszynach wirtualnych platformy Azure i kopii zapasowej platformy Azure.](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrona danych kopii zapasowej przed niezamierzonymi usunięciami

Usługa Azure Backup udostępnia funkcje zabezpieczeń ułatwiające ochronę danych kopii zapasowej nawet po usunięciu. Po usunięciu nietrwałym, jeśli użytkownik usunie kopię zapasową maszyny Wirtualnej, dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwania nietrwałego" nie wiąże się z żadnymi kosztami dla klienta. [Dowiedz się więcej o usuwaniu nietrwałym](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorowanie i ostrzeganie o podejrzanych działaniach

Usługa Azure Backup udostępnia [wbudowane funkcje monitorowania i alertów](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) w celu wyświetlania i konfigurowania akcji dla zdarzeń związanych z usługą Azure Backup. [Raporty kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports) służą jako miejsce docelowe do śledzenia użycia, inspekcji kopii zapasowych i przywracania oraz identyfikowania kluczowych trendów na różnych poziomach szczegółowości. Korzystanie z narzędzi do monitorowania i raportowania usługi Azure Backup może ostrzegać o wszelkich nieautoryzowanych, podejrzanych lub złośliwych działaniach, gdy tylko wystąpią.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funkcje zabezpieczeń ułatwiające ochronę hybrydowych kopii zapasowych

Usługa Azure Backup używa agenta usług odzyskiwania platformy Microsoft Azure (MARS) do tworzenia kopii zapasowych i przywracania plików, folderów oraz woluminu lub stanu systemu z komputera lokalnego na platformie Azure. Mars udostępnia teraz funkcje zabezpieczeń ułatwiające ochronę hybrydowych kopii zapasowych. Między innymi są to następujące funkcje:

* Dodatkowa warstwa uwierzytelniania jest dodawana za każdym razem, gdy wykonywana jest operacja krytyczna, taka jak zmiana hasła. Ta weryfikacja ma na celu zapewnienie, że takie operacje mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure. [Dowiedz się więcej o funkcjach zapobiegające atakom](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* Usunięte dane kopii zapasowej są przechowywane przez dodatkowe 14 dni od daty usunięcia. Zapewnia to możliwość odzyskania danych w danym okresie czasu, więc nie ma utraty danych, nawet jeśli dojdzie do ataku. Ponadto większa liczba minimalnych punktów odzyskiwania są utrzymywane w celu ochrony przed uszkodzonymi danymi. [Dowiedz się więcej o odzyskiwaniu usuniętych danych kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

## <a name="compliance-with-standardized-security-requirements"></a>Zgodność ze znormalizowanymi wymogami bezpieczeństwa

Aby pomóc organizacjom w spełnieniu krajowych, regionalnych i branżowych wymagań dotyczących gromadzenia i wykorzystywania danych osób fizycznych, usługa Microsoft Azure & Azure Backup oferuje kompleksowy zestaw certyfikatów i certyfikatów. [Zobacz listę certyfikatów zgodności](compliance-offerings.md)

## <a name="next-steps"></a>Następne kroki

* [Funkcje zabezpieczeń ułatwiające ochronę obciążeń w chmurze korzystających z usługi Azure Backup](backup-azure-security-feature-cloud.md)
* [Funkcje zabezpieczeń ułatwiające ochronę hybrydowych kopii zapasowych korzystających z usługi Azure Backup](backup-azure-security-feature.md)
