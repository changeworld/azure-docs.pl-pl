---
title: Skrytka klienta dla platformy Microsoft Azure
description: Techniczne omówienie skrytki klienta dla platformy Microsoft Azure, która zapewnia kontrolę nad dostępem dostawcy chmury, gdy firma Microsoft może potrzebować dostępu do danych klienta.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561973"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Skrytka klienta dla platformy Microsoft Azure

> [!NOTE]
> Aby korzystać z tej funkcji, organizacja musi mieć [plan pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) z minimalnym poziomem **dewelopera.**

Skrytka klienta dla platformy Microsoft Azure udostępnia klientom interfejs do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych klienta. Jest on używany w przypadkach, gdy inżynier firmy Microsoft musi uzyskać dostęp do danych klienta podczas żądania pomocy technicznej.

W tym artykule opisano, jak żądania skrytki klienta są inicjowane, śledzone i przechowywane do późniejszych przeglądów i inspekcji.

Skrytka klienta jest teraz ogólnie dostępna i obecnie włączona dla zdalnego dostępu pulpitu do maszyn wirtualnych.

## <a name="workflow"></a>Przepływ pracy

W poniższych krokach opisano typowy przepływ pracy dla żądania skrytki klienta.

1. Ktoś w organizacji ma problem z obciążeniem platformy Azure.

2. Po tym, jak ta osoba rozwiązuje problem, ale nie może go rozwiązać, otwiera bilet pomocy technicznej w [witrynie Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Bilet jest przypisany do inżyniera obsługi klienta platformy Azure.

3. Inżynier pomocy technicznej platformy Azure przegląda żądanie usługi i określa kolejne kroki, aby rozwiązać problem.

4. Jeśli inżynier pomocy technicznej nie może rozwiązać problemu przy użyciu standardowych narzędzi i danych telemetrycznych, następnym krokiem jest żądanie podwyższonych uprawnień przy użyciu usługi dostępu just-in-time (JIT). To żądanie może pochodzić od oryginalnego inżyniera pomocy technicznej. Lub może być z innego inżyniera, ponieważ problem jest eskalowany do zespołu Azure DevOps.

5. Po przesłaniu żądania dostępu przez inżyniera platformy Azure usługa Just-In-Time ocenia żądanie, biorąc pod uwagę takie czynniki, jak:
    - Zakres zasobu
    - Czy żądający jest tożsamością izolową, czy też za pomocą uwierzytelniania wieloskładnikowego
    - Poziomy uprawnień

    Na podstawie reguły JIT to żądanie może również zawierać zatwierdzenie przez wewnętrzne osoby zatwierdzające firmy Microsoft. Na przykład osoba zatwierdzająca może być potencjalnym klientem lub Menedżerem DevOps.

6. Gdy żądanie wymaga bezpośredniego dostępu do danych klienta, jest inicjowane żądanie skrytki klienta. Na przykład zdalny dostęp pulpitu do maszyny wirtualnej klienta.

    Żądanie jest teraz w stanie **powiadomiony klient,** czekając na zatwierdzenie klienta przed udzieleniem dostępu.

7. W organizacji klienta użytkownik, który ma [rolę Właściciela](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) dla subskrypcji platformy Azure, otrzymuje wiadomość e-mail od firmy Microsoft, aby powiadomić go o oczekującym żądaniu dostępu. W przypadku żądań skrytki klienta ta osoba jest wyznaczoną osobą zatwierdzacą.

    Przykładowy adres e-mail:

    ![Skrytka klienta platformy Azure — powiadomienie e-mail](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. Powiadomienie e-mail zawiera łącze do bloku **skrytki klienta** w witrynie Azure portal. Korzystając z tego łącza, wyznaczony osoba zatwierdzająca loguje się do witryny Azure Portal, aby wyświetlić wszystkie oczekujące żądania, które ich organizacja ma dla skrytki klienta:

    ![Skrytka klienta platformy Azure — strona docelowa](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Żądanie pozostaje w kolejce klienta przez cztery dni. Po tym czasie żądanie dostępu automatycznie wygasa i nie ma dostępu do inżynierów firmy Microsoft.

9. Aby uzyskać szczegółowe informacje o oczekującym żądaniu, wyznaczona osoba zatwierdzająca może wybrać żądanie skrytki z **oczekujących żądań:**

    ![Skrytka klienta platformy Azure — wyświetl oczekujące żądanie](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Wyznaczona osoba zatwierdzająca może również wybrać **identyfikator żądania usługi,** aby wyświetlić żądanie biletu pomocy technicznej utworzone przez oryginalnego użytkownika. Te informacje zawierają kontekst, dlaczego pomoc techniczna firmy Microsoft jest zaangażowana, oraz historię zgłoszonego problemu. Przykład:

    ![Skrytka klienta platformy Azure — wyświetl żądanie biletu pomocy technicznej](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Po przejrzeniu żądania wyznaczona osoba zatwierdzająca wybiera **opcję Zatwierdź** lub **Odmów:**

    ![Skrytka klienta platformy Azure — wybierz opcję Zatwierdź lub odmów](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    W wyniku wyboru:
    - **Zatwierdź:** Dostęp jest przyznawany inżynierowi firmy Microsoft. Dostęp jest przyznawany na domyślny okres ośmiu godzin.
    - **Odmów:** Żądanie podwyższonego dostępu przez inżyniera firmy Microsoft zostanie odrzucone i nie zostaną podjęte żadne dalsze działania.

Do celów inspekcji akcje podjęte w tym przepływie pracy są rejestrowane w [dziennikach żądań skrytki klienta](#auditing-logs).

## <a name="auditing-logs"></a>Dzienniki inspekcji

Dzienniki skrytki klienta są przechowywane w dziennikach aktywności. W witrynie Azure portal wybierz **dzienniki aktywności,** aby wyświetlić informacje inspekcji związane z żądaniami skrytki klienta. Można filtrować pod kątem określonych akcji, takich jak:
- **Żądanie odmów skrytki**
- **Utwórz żądanie skrytki**
- **Zatwierdź żądanie skrytki**
- **Wygaśnięcie żądania skrytki**

Przykład:

![Skrytka klienta platformy Azure — dzienniki aktywności](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Obsługiwane usługi i scenariusze w ogólnej dostępności

Następujące usługi i scenariusze są obecnie w ogólnej dostępności dla skrytki klienta.

### <a name="remote-desktop-access-to-virtual-machines"></a>Dostęp pulpitu zdalnego do maszyn wirtualnych

Skrytka klienta jest obecnie włączona dla żądań dostępu do pulpitu zdalnego do maszyn wirtualnych. Obsługiwane są następujące obciążenia:
- Platforma jako usługa (PaaS) — usługi w chmurze Azure (rola sieci Web i rola procesu roboczego)
- Infrastruktura jako usługa (IaaS) — Windows i Linux (tylko usługa Azure Resource Manager)
- Zestaw skalowania maszyny wirtualnej - Windows i Linux

> [!NOTE]
> Wystąpienia IaaS Classic nie są obsługiwane przez skrytki klienta. Jeśli masz obciążenia uruchomione w wystąpieniach IaaS Classic, zaleca się ich migrację z klasycznych do modeli wdrażania Menedżera zasobów. Aby uzyskać instrukcje, zobacz [Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Szczegółowe dzienniki inspekcji

W scenariuszach, które obejmują dostęp do pulpitu zdalnego, można użyć dzienników zdarzeń systemu Windows, aby przejrzeć akcje podjęte przez inżyniera firmy Microsoft. Rozważ użycie usługi Azure Security Center do zbierania dzienników zdarzeń i kopiowania danych do obszaru roboczego do analizy. Aby uzyskać więcej informacji, zobacz [Zbieranie danych w usłudze Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Obsługiwane usługi i scenariusze w wersji zapoznawczej

Następujące usługi są obecnie w wersji zapoznawczej dla skrytki klienta:

- Azure Storage

- Azure SQL DB

- Azure Data Explorer

- Maszyny wirtualne (teraz również obejmujące dostęp do zrzutów pamięci i dysków zarządzanych)

- Transfery subskrypcji platformy Azure

Aby włączyć skrytki klienta dla tych ofert w wersji zapoznawczej dla swojej organizacji, zarejestruj się w [skrytki klienta dla publicznej wersji zapoznawczej platformy Azure](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Wykluczenia

Żądania skrytki klienta nie są wyzwalane w następujących scenariuszach pomocy technicznej:

- Inżynier firmy Microsoft musi wykonać działanie, które wykracza poza standardowe procedury operacyjne. Na przykład, aby odzyskać lub przywrócić usługi w nieoczekiwanych lub nieprzewidywalnych scenariuszach.

- Inżynier firmy Microsoft uzyskuje dostęp do platformy Azure w ramach rozwiązywania problemów i przypadkowo ma dostęp do danych klienta. Na przykład zespół sieci azure wykonuje rozwiązywanie problemów, które powoduje przechwytywanie pakietów na urządzeniu sieciowym. Jeśli jednak klient zaszyfrował dane podczas przesyłania, inżynier nie może odczytać danych.

## <a name="next-steps"></a>Następne kroki

Skrytka klienta jest automatycznie dostępna dla wszystkich klientów, którzy mają [plan pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) z minimalnym poziomem **dewelopera.**

Jeśli masz kwalifikujący się plan pomocy technicznej, nie musisz podjąć żadnych działań w celu włączenia skrytki klienta. Żądania skrytki klienta są inicjowane przez inżyniera firmy Microsoft, jeśli ta akcja jest potrzebna do postępu biletu pomocy technicznej, który jest złożony od kogoś w organizacji.
