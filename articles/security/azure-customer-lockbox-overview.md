---
title: Skrytki klienta na platformie Microsoft Azure
description: Techniczne omówienie skrytki klienta dla systemu Microsoft Azure, która zapewnia kontrolę nad dostęp do dostawcy chmury w przypadku firmy Microsoft może być konieczne do dostępu do danych klienta.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 06/20/2019
ms.openlocfilehash: 0ee2dde5a941d069f5b745eafb35df780f657a47
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312623"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Skrytki klienta na platformie Microsoft Azure

> [!NOTE]
> Aby użyć tej funkcji, organizacja musi mieć [planu pomocy technicznej Azure](https://azure.microsoft.com/support/plans/) z minimalnym poziomem **Developer**.

Skrytki klienta dla systemu Microsoft Azure udostępnia interfejs dla klientów przejrzeć i zatwierdzić lub odrzucić żądań dostępu do danych klienta. Jest on używany w przypadkach, gdy inżynier z firmy Microsoft musi uzyskać dostęp do danych klienta podczas żądania pomocy technicznej.

W tym artykule opisano, jak żądania do skrytki klienta są inicjowane, śledzone i przechowywane przez nowszy przeglądów i inspekcji.

Skrytki klienta jest teraz ogólnie dostępna i aktualnie włączonego dostępu pulpitu zdalnego do maszyn wirtualnych.

## <a name="workflow"></a>Przepływ pracy

Następujące kroki przedstawiają typowy przepływ pracy dla żądania do skrytki klienta.

1. Ktoś w organizacji istnieje problem z ich obciążenie platformy Azure.

2. Po tej osoby rozwiązuje ten problem, ale nie można go naprawić, otwarciu biletu pomocy technicznej z [witryny Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). --Ticket jest przypisany do ze specjalistą pomocy technicznej platformy Azure klienta.

3. Ze specjalistą pomocy technicznej platformy Azure monitoruje żądania obsługi i określa następnych kroków, aby rozwiązać ten problem.

4. Jeśli inżynier pomocy technicznej nie może rozwiązać ten problem przy użyciu standardowych narzędzi i danych telemetrycznych, następnym krokiem jest żądanie podniesionego poziomu uprawnień przy użyciu usługi Dostęp just in Time (JIT). To żądanie może być z oryginalnego ze specjalistą pomocy technicznej. Lub może być z różnych inżynier, ponieważ eskalacji problemu do zespołu DevOps platformy Azure.

5. Po dostępu zostało przesłane żądanie przez inżyniera platformy Azure, Just-In-Time service daje w wyniku żądania, biorąc pod uwagę czynniki, takie jak:
    - Zakres zasobów
    - Czy strona żądająca jest izolowane tożsamość lub za pomocą uwierzytelniania wieloskładnikowego
    - Poziomy uprawnień
    
    Zgodnie z regułą JIT, to żądanie może również obejmować zatwierdzenia z wewnętrznego Microsoft osób zatwierdzających. Na przykład osoba zatwierdzająca może być wyprzedzenia pomocy technicznej klient lub kierownik działu DevOps.

6. Jeśli żądanie wymaga bezpośredniego dostępu do danych klienta, jest inicjowane żądania do skrytki klienta. Na przykład pulpitu dostęp zdalny do maszyny wirtualnej klienta.
    
    Żądanie jest teraz w **powiadomienie klienta** stanu, oczekiwanie na zatwierdzenie przez klienta przed udzieleniem im dostępu.

7. W organizacji klienta, użytkownik, który ma [roli właściciel](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) Azure subskrypcja otrzymuje wiadomość e-mail od firmy Microsoft w celu powiadomienia ich o żądaniu dostępu oczekujące. W przypadku żądania do skrytki klienta ta osoba jest wyznaczonej osoby zatwierdzającej.
    
    Przykładową wiadomością e-mail:
    
    ![Usługa Azure skrytki klienta — powiadomienia e-mail](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. Powiadomienie e-mail z linkiem do **skrytki klienta** bloku w witrynie Azure portal. Korzystając z tego linku, wyznaczonej osoby zatwierdzającej loguje się do witryny Azure portal można wyświetlić wszystkie oczekujące żądania, które organizacja użytkownika ma do skrytki klienta:
    
    ![Usługa Azure skrytki klienta — strona docelowa](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   Żądanie pozostaje w kolejce klienta cztery dni. Po upływie tego czasu żądania dostępu automatycznie wygasa i nie dostęp do inżynierów firmy Microsoft.

9. Aby uzyskać szczegółowe informacje o oczekującego żądania, wyznaczonej osoby zatwierdzającej można wybrać żądanie skrytki **oczekujących żądań**:
    
    ![Usługa Azure skrytki klienta - Wyświetl oczekującego żądania](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. Można również wybrać opcję wyznaczonej osoby zatwierdzającej **identyfikator ŻĄDANIA usługi** Aby wyświetlić żądanie biletu pomocy technicznej, który został utworzony przez użytkownika oryginalnego. Informacje te stanowią kontekst Dlaczego Support firmy Microsoft jest zaangażowana i historię zgłoszonego problemu. Na przykład:
    
    ![Usługa Azure skrytki klienta - Wyświetl żądanie biletu pomocy technicznej](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. Po zapoznaniu się z żądania, wybiera wyznaczonej osoby zatwierdzającej **Zatwierdź** lub **Odmów**:
    
    ![Skrytki klienta platformy Azure — wybierz opcję Zatwierdź lub Odmów](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    W wyniku zaznaczenie:
    - **Zatwierdź**:  Dostęp jest udzielany, aby inżynier z firmy Microsoft. Dostęp jest udzielany przez okres domyślny osiem godzin.
    - **Odmów**: Odrzucenia żądania dostępu z podwyższonym poziomem uprawnień, inżynier z firmy Microsoft i są podejmowane żadne dalsze działania.

Na potrzeby inspekcji, akcje wykonywane w tym przepływie pracy są rejestrowane w [dzienniki żądania skrytki klienta](#auditing-logs).

## <a name="auditing-logs"></a>Dzienniki inspekcji

Dzienniki skrytki klienta są przechowywane w dziennikach aktywności. W witrynie Azure portal wybierz **dzienników aktywności** do wyświetlania informacji inspekcji, związanych z żądania do skrytki klienta. Możesz zastosować filtr dla określonych akcji, takich jak:
- **Odmowa żądania do skrytki**
- **Tworzenie żądania do skrytki**
- **Zatwierdź żądania do skrytki**
- **Żądania do skrytki wygaśnięcia**

Na przykład:

![Usługa Azure skrytki klienta - dzienników aktywności](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Obsługiwane usługi i scenariusze

Poniższe usługi i scenariusze są obecnie ogólnie dostępne do skrytki klienta.

### <a name="remote-desktop-access-to-virtual-machines"></a>Dostępu do pulpitu zdalnego do maszyn wirtualnych

Skrytki klienta jest obecnie włączona dla żądań dostępu do usług pulpitu zdalnego do maszyn wirtualnych. Obsługiwane są następujące obciążenia:
- Platforma jako usługa (PaaS) — usługi Azure Cloud Services (role sieci web i roli proces roboczy)
- Infrastruktura jako usługa (IaaS) — Windows i Linux (tylko w usłudze Azure Resource Manager)
- Zestaw skalowania maszyn wirtualnych — Windows i Linux

> [!NOTE]
> Wystąpienia z klasycznego środowiska IaaS nie są obsługiwane przez skrytki klienta. W przypadku obciążeń działających w wystąpieniach z klasycznego środowiska IaaS zalecamy ich migracja z modelu klasycznego do modelu wdrażania usługi Resource Manager. Aby uzyskać instrukcje, zobacz [obsługiwanej przez platformę migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Szczegółowych dzienników inspekcji

W przypadku scenariuszy obejmujących dostęp do pulpitu zdalnego umożliwia dzienniki zdarzeń Windows Przejrzyj akcje wykonywane przez inżynier z firmy Microsoft. Należy rozważyć użycie usługi Azure Security Center zbieranie dzienników zdarzeń i skopiować dane do swojego obszaru roboczego do analizy. Aby uzyskać więcej informacji, zobacz [zbierania danych w usłudze Azure Security Center](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Wykluczenia

Żądania do skrytki klienta nie są wyzwalane w następujących scenariuszach inżynierów pomocy technicznej:

- Inżynier z firmy Microsoft potrzebuje do wykonywania działań, która wykracza poza standardowe procedury operacyjne. Na przykład, aby odzyskać lub przywracanie w scenariuszach nieoczekiwany lub nieprzewidywalne.

- Inżynier z firmy Microsoft uzyskuje dostęp do platformy Azure w ramach procedury rozwiązywania problemów i przypadkowo ma dostęp do danych klienta. Na przykład zespół sieci platformy Azure wykonuje, rozwiązywania problemów, jeśli skutkuje przechwytywania pakietów w urządzeniu sieciowym. Jednak jeśli klient zaszyfrowane dane podczas przesyłania, inżynier ds. nie można odczytać danych.

## <a name="next-steps"></a>Kolejne kroki

Skrytki klienta jest automatycznie dostępne dla wszystkich klientów, którzy mają [planu pomocy technicznej Azure](https://azure.microsoft.com/support/plans/) z minimalnym poziomem **Developer**.

W przypadku planu pomocy technicznej kwalifikują żadnych czynności przez użytkownika umożliwiające skrytki klienta. Żądania do skrytki klienta są inicjowane przez inżynier z firmy Microsoft, jeśli ta akcja nie jest konieczne postępu bilet pomocy technicznej, który jest zachowane pochodzącej od osób w Twojej organizacji.
