---
title: Administracja przy użyciu witryny Azure EA Portal
description: W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 7b594cef2fefa164ef900cbfd65fcf95d5d47b94
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901138"
---
# <a name="azure-ea-portal-administration"></a>Administracja przy użyciu witryny Azure EA Portal

W tym artykule opisano typowe zadania wykonywane przez administratora w witrynie Azure EA Portal (https://ea.azure.com). Witryna Azure EA Portal to portal zarządzania online, który ułatwia klientom zarządzanie kosztami usług umowy EA na platformie Azure. Aby uzyskać informacje wprowadzające dotyczące witryny Azure EA Portal, zobacz artykuł [Rozpoczynanie pracy w witrynie Azure EA Portal](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Dodawanie nowego administratora przedsiębiorstwa

Administratorzy przedsiębiorstwa mają największe uprawnienia podczas zarządzania rejestracją w portalu Azure EA. Początkowy administrator platformy Azure EA został utworzony podczas konfigurowania umowy EA. Można jednak dodawać i usuwać nowych administratorów w dowolnym momencie. Nowi administratorzy są dodawani tylko przez istniejących administratorów. Aby uzyskać więcej informacji na temat dodawania dodatkowych administratorów przedsiębiorstwa, zobacz sekcję [Tworzenie innego administratora przedsiębiorstwa](billing-ea-portal-get-started.md#create-another-enterprise-admin). Aby uzyskać więcej informacji o rolach i zadaniach dotyczących rozliczeń, zobacz sekcję [Role i zadania profilów rozliczeniowych](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Aktualizowanie stanu użytkownika z Oczekujący na Aktywny

Gdy nowi właściciele konta są po raz pierwszy dodawani do rejestracji w ramach umowy EA, ich stan jest wyświetlany jako _oczekujące_. Gdy nowy właściciel konta otrzymuje powitalną wiadomość e-mail z informacjami o aktywacji, może się zalogować, aby aktywować swoje konto. Po aktywowaniu konta stan konta jest aktualizowany z _oczekujące_ na _aktywne_. Nowi użytkownicy mogą zobaczyć monit o wprowadzenie imienia i nazwiska, aby utworzyć konto komercyjne. W takiej sytuacji muszą dodać wymagane informacje, aby kontynuować pracę, a konto jest aktywowane.

## <a name="add-a-department-admin"></a>Dodawanie administratora działu

Po utworzeniu działu przez administratora portalu Azure EA administrator oferty Azure Enterprise może dodać administratorów działu i skojarzyć ich z działem. Administrator działu może tworzyć nowe konta. Nowe konta są potrzebne do tworzenia subskrypcji portalu Azure EA.

Aby uzyskać więcej informacji na temat dodawania działu, zobacz temat dotyczący tworzenia działu w portalu Azure EA.

## <a name="enterprise-user-roles"></a>Role użytkownika przedsiębiorstwa

Witryna Azure EA Portal ułatwia administrowanie kosztami i użyciem dla umowy EA platformy Azure. W witrynie Azure EA Portal istnieją trzy główne role:

- Administrator umowy EA
- Administrator działu
- Właściciel konta

Każda rola ma inny poziom dostępu i uprawnień.

Aby uzyskać więcej informacji na temat ról użytkownika, zobacz temat dotyczący ról użytkownika przedsiębiorstwa.

## <a name="add-an-azure-ea-account"></a>Dodawanie konta usługi Azure EA

Konto umowy Azure EA to jednostka organizacyjna w witrynie Azure EA Portal, która jest używana do administrowania subskrypcjami, a do raportowania. Aby uzyskać dostęp do usług platformy Azure i korzystać z nich, musisz utworzyć konto lub poprosić o jego utworzenie.

Aby uzyskać więcej informacji na temat kont platformy Azure, zobacz temat dotyczący dodawania konta.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Przenoszenie konta przedsiębiorstwa do nowej rejestracji

Podczas przenoszenia konta przedsiębiorstwa do nowej rejestracji pamiętaj o następujących kwestiach:

- Przenoszone są tylko konta określone w żądaniu. Jeśli wybierzesz wszystkie konta, wszystkie zostaną przeniesione.
- Rejestracja źródłowa zachowuje stan jako aktywny lub rozszerzony. Z rejestracji można korzystać do momentu jej wygaśnięcia.

### <a name="effective-transfer-date"></a>Data obowiązywania przeniesienia

Data obowiązywania przeniesienia może być datą początkową rejestracji, do której chcesz dokonać przeniesienia, lub późniejszą. Przenoszona rejestracja to _rejestracja docelowa_. Po przeniesieniu konta wszystkie informacje dotyczące użycia na koncie przed datą obowiązywania przeniesienia pozostają w obrębie rejestracji, z której następuje przeniesienie. Rejestracja, z której przenosisz, to _rejestracja źródłowa_.  Użycie rejestracji źródłowej jest obciążane opłatą w odniesieniu do zobowiązania pieniężnego lub jako nadwyżka. Użycie, które następuje po dacie obowiązywania przeniesienia, jest transferowane do nowej rejestracji i są naliczane odpowiednie opłaty.

Datę przeniesienia konta można cofnąć nawet do daty początkowej rejestracji docelowej. Albo do daty rozpoczęcia obowiązywania rejestracji źródłowej.

### <a name="monetary-commitment"></a>Zobowiązanie pieniężne

Zobowiązania pieniężnego nie można przenosić między rejestracjami. Salda zobowiązania pieniężnego są powiązane z umową w ramach rejestracji, w której zostały zamówione. Zobowiązanie pieniężne nie jest przenoszone jako część procesu przenoszenia konta lub rejestracji.

### <a name="services-affected"></a>Uwzględnione usługi

Podczas przenoszenia konta nie występują przestoje. Można je wykonać w tym samym dniu żądania, jeśli zostaną podane wszystkie wymagane informacje.

### <a name="prerequisites"></a>Wymagania wstępne

Po zażądaniu przeniesienia konta podaj następujące informacje:


- Nazwa konta i identyfikator właściciela konta do przeniesienia
- W przypadku rejestracji źródłowej numer rejestracji i konto do przeniesienia
- W przypadku rejestracji docelowej numer rejestracji, do której nastąpi przeniesienie
- W przypadku daty obowiązywania przeniesienia konta może to być data początkowa rejestracji docelowej lub późniejsza

Inne zagadnienia, które należy wziąć pod uwagę przed przeniesieniem konta:

- Zatwierdzenie od administratora umowy EA jest wymagane w przypadku rejestracji docelowej i źródłowej
  - W niektórych przypadkach firma Microsoft może zażądać dodatkowego zatwierdzenia od administratora umowy EA z rejestracji źródłowej
- Jeśli przeniesienie konta nie spełnia Twoich wymagań, rozważ przeniesienie rejestracji.
- Przeniesienie konta powoduje przeniesienie wszystkich usług, subskrypcji, kont, działów i całej struktury rejestracji, w tym wszystkich administratorów działu umów EA.
- Po przeniesieniu konta stan rejestracji źródłowej jest ustawiany na _Przeniesione_. Przeniesione konto jest dostępne wyłącznie dla celów raportowania użycia historycznego.
- Do rejestracji ze stanem Przeniesiona nie można dodawać ról ani subskrypcji. Stan uniemożliwia dodatkowe użycie w odniesieniu do rejestracji.
- Pozostałe saldo zobowiązania pieniężnego w umowie źródłowej zostanie utracone, łącznie z przyszłymi okresami.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Przenoszenie rejestracji przedsiębiorstwa do nowej rejestracji

Po utworzeniu żądania dotyczącego przeniesienia całej rejestracji w przedsiębiorstwie do rejestracji zostaną wykonane następujące akcje:

- Wszystkie usługi, subskrypcje, konta, działy i cała struktura rejestracji, w tym wszyscy administratorzy działu umów EA, zostaną przeniesione.
- Stan rejestracji zostanie ustawiono na _Przeniesione_. Przeniesiona rejestracja jest dostępna wyłącznie dla celów raportowania użycia historycznego.
- Do przeniesionej rejestracji nie można dodawać ról ani subskrypcji. Stan Przeniesione uniemożliwia dodatkowe użycie w odniesieniu do rejestracji.
- Pozostałe saldo zobowiązania pieniężnego w umowie zostanie utracone, łącznie z przyszłymi okresami.

### <a name="effective-transfer-date"></a>Data obowiązywania przeniesienia

Data obowiązywania przeniesienia może być datą początkową rejestracji, którą chcesz przenieść do rejestracji docelowej, lub późniejszą.

Użycie rejestracji źródłowej jest obciążane opłatą w odniesieniu do zobowiązania pieniężnego lub jako nadwyżka. Użycie, które następuje po dacie obowiązywania przeniesienia, jest transferowane do nowej rejestracji i są naliczane odpowiednie opłaty.

### <a name="effective-transfer-date-in-the-past"></a>Data obowiązywania przeniesienia w przeszłości

Datę przeniesienia konta można cofnąć nawet do daty początkowej rejestracji docelowej. Albo do daty rozpoczęcia obowiązywania rejestracji źródłowej.

### <a name="monetary-commitment"></a>Zobowiązanie pieniężne

Zobowiązania pieniężnego nie można przenosić między rejestracjami. Salda zobowiązania pieniężnego są powiązane z umową w ramach rejestracji, w której zostały zamówione. Zobowiązanie pieniężne nie jest przenoszone jako część procesu przenoszenia konta lub rejestracji.

### <a name="services-affected"></a>Uwzględnione usługi

Podczas przenoszenia konta nie występują przestoje. Można je wykonać w tym samym dniu żądania, jeśli zostaną podane wszystkie wymagane informacje.

### <a name="prerequisites"></a>Wymagania wstępne

Po zażądaniu przeniesienia rejestracji podaj następujące informacje:

- W przypadku rejestracji źródłowej numer rejestracji i konto do przeniesienia
- W przypadku rejestracji docelowej numer rejestracji, do której nastąpi przeniesienie
- W przypadku daty obowiązywania przeniesienia rejestracji może to być data początkowa rejestracji docelowej lub późniejsza. Wybrana data nie może wpływać na użycie dla żadnej wystawionej już faktury nadwyżkowej.

Inne zagadnienia, które należy wziąć pod uwagę przed przeniesieniem rejestracji:

- Zatwierdzenie od administratora umowy EA jest wymagane w przypadku rejestracji docelowej i źródłowej
  - W niektórych przypadkach firma Microsoft może zażądać dodatkowego zatwierdzenia od administratora umowy EA z rejestracji źródłowej
- Jeśli przeniesienie rezerwacji nie spełnia Twoich wymagań, rozważ przeniesienie konta.
- Tylko wybrane przez Ciebie konta są przenoszone. Możesz poprosić o przeniesienie wszystkich kont.
- Rejestracja źródłowa zachowuje stan jako aktywny/rozszerzony. Z rejestracji można korzystać do momentu jej wygaśnięcia.

## <a name="change-account-owner"></a>Zmienianie właściciela konta

W witrynie Azure EA Portal można przenosić subskrypcje od jednego właściciela konta do innego. Aby uzyskać więcej informacji, zobacz sekcję [Zmienianie właściciela konta](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Efekty przeniesienia subskrypcji

Jeśli subskrypcja platformy Azure zostanie przeniesiona na konto w tej dzierżawie usługi Azure Active Directory, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [kontrolą dostępu na podstawie (RBAC)](../role-based-access-control/overview.md) używaną do zarządzania zasobami, zachowają dostęp.

Aby wyświetlić użytkowników z dostępem RBAC do subskrypcji:

1. W witrynie Azure Portal otwórz pozycję **Subskrypcje**.
2. Wybierz subskrypcję, którą chcesz wyświetlić, a następnie wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** .
3. Wybierz pozycję **Przypisania ról**. Na stronie przypisań ról są wyświetlani wszyscy użytkownicy z dostępem RBAC do subskrypcji.

Jeśli subskrypcja zostanie przeniesiona na konto w innej dzierżawie usługi Azure AD, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [dostępem RBAC](../role-based-access-control/overview.md) do zarządzania zasobami _utracą_ dostęp. Chociaż dostęp do usługi RBAC nie istnieje, dostęp do subskrypcji może być dostępny w ramach mechanizmów zabezpieczeń, takich jak przykład:

- Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Aby uzyskać więcej informacji, zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../cloud-services/cloud-services-certs-create.md).
- Klucze dostępu dla usług, takich jak Storage. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md).
- Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do swoich zasobów platformy Azure, powinien rozważyć zaktualizowanie wszystkich wpisów tajnych skojarzonych z usługą. Większość zasobów można zaktualizować, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum wybierz pozycję **Wszystkie zasoby**.
3. Wybierz zasób.
4. Na stronie zasobu kliknij pozycję **Ustawienia**, aby wyświetlić i zaktualizować istniejące wpisy tajne.



## <a name="close-an-azure-enterprise-enrollment"></a>Zamykanie rejestracji platformy Azure w przedsiębiorstwie

Jeśli chcesz zamknąć rejestrację w portalu Azure EA, możesz:

- Anulować wszystkie subskrypcje skojarzone z umową Azure EA w witrynie Azure Portal.
- Skontaktować się z doradcą oprogramowania lub partnerem klienta i poprosić go o zamknięcie umowy Enterprise Agreement platformy Azure.

## <a name="update-notification-settings"></a>Aktualizowanie ustawień powiadomień

Administratorzy przedsiębiorstwa są rejestrowani automatycznie w celu otrzymywania powiadomień dotyczących użycia skojarzonych z ich rejestracją. Każdy administrator przedsiębiorstwa może zmienić interwał poszczególnych powiadomień lub może je całkowicie wyłączyć.

Kontakty dla powiadomień są wyświetlane w witrynie Azure EA Portal w sekcji **Kontakt dla powiadomień**. Zarządzanie kontaktami dla powiadomień zapewnia, że odpowiednie osoby w organizacji otrzymują powiadomienia portalu Azure EA.

Aby wyświetlić bieżące ustawienia powiadomień:

1. W witrynie Azure EA Portal przejdź do okna **Zarządzanie** > **Kontakt dla powiadomień**.
2. Adres e-mail — adres e-mail skojarzony z kontem Microsoft lub kontem służbowym administratora przedsiębiorstwa, który otrzymuje powiadomienia.
3. Częstotliwość powiadamiania o nierozliczonym saldzie — ustawiona częstotliwość, z jaką powiadomienia są wysyłane do każdego administratora przedsiębiorstwa.

Aby dodać kontakt:

1. Kliknij pozycję **+Dodaj kontakt**.
2. Wprowadź adres e-mail, a następnie potwierdź go.
3. Kliknij pozycję **Zapisz**.

Nowy kontakt dla powiadomień zostanie wyświetlony w sekcji **Kontakt dla powiadomień**. Aby zmienić częstotliwość powiadamiania, wybierz kontakt dla powiadomień i kliknij symbol ołówka po prawej stronie wybranego wiersza. Ustaw częstotliwość na **codziennie**, **co tydzień**, **co miesiąc** lub**brak**.

Możesz pominąć powiadomienia dotyczące cyklu życia ze _zbliżającą się datą zakończenia okresu pokrycia_ oraz _ze zbliżającą się datą wyłączenia i anulowania aprowizacji_. Wyłączenie powiadomień dotyczących cyklu życia powoduje pomijanie powiadomień o okresie pokrycia i dacie zakończenia obowiązywania umowy.

## <a name="manage-partner-notifications"></a>Zarządzanie powiadomieniami partnerów

Administratorzy partnerów mogą zarządzać częstotliwością otrzymywania powiadomień o użyciu we własnych rejestracjach. Automatycznie otrzymują oni powiadomienia dotyczące nierozliczonego salda. Mogą zmieniać częstotliwość poszczególnych powiadomień na powiadomienia comiesięczne, cotygodniowe lub codzienne albo wyłączać je całkowicie.

Jeśli użytkownik nie otrzymuje powiadomienia, sprawdź, czy ustawienia powiadomień użytkownika są poprawne, wykonując poniższe kroki.

1. Zaloguj się do witryny Azure EA Portal jako administrator partnera.
2. Kliknij pozycję **Zarządzanie**, a następnie kliknij kartę **Partner**.
3. Przejrzyj listę administratorów w sekcji **Administrator**.
4. Aby edytować preferencje powiadomień, umieść kursor nad odpowiednim administratorem i kliknij symbol ołówka.
5. Zwiększ częstotliwość powiadamiania i zmodyfikuj powiadomienia dotyczące cyklu życia zgodnie z wymaganiami.
6. W razie potrzeby dodaj kontakt, a następnie kliknij przycisk **Dodaj**.
7. Kliknij pozycję **Zapisz**.

![Przykład pokazujący dodawanie kontaktu ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Oferta dostępu sponsorowanego Azure
Oferta dostępu sponsorowanego Azure to ograniczone, sponsorowane konto platformy Microsoft Azure. Jest ono dostępne tylko na zaproszenie e-mail dla ograniczonej liczby klientów wybranych przez firmę Microsoft. Jeśli masz upoważnienie do oferty dostępu sponsorowanego Microsoft Azure, otrzymasz zaproszenie e-mail oferty dla Twojego identyfikatora konta.
Aby uzyskać więcej informacji, zobacz:

- Omówienie oferty dostępu sponsorowanego — https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- Portal salda dostępu sponsorowanego — https://www.microsoftazuresponsorships.com/balance  
- Zewnętrzne pytania i odpowiedzi dotyczące dostępu sponsorowanego — https://azuresponsorships-staging.azurewebsites.net/faq
- Wniosek o pomoc techniczną dotyczącą aktywacji dostępu sponsorowanego — http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Następne kroki
- Przeczytaj więcej o tym, jak [rezerwacje maszyn wirtualnych](billing-ea-portal-vm-reservations.md) mogą pomóc Ci zaoszczędzić pieniądze.
- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](billing-ea-portal-troubleshoot.md).
