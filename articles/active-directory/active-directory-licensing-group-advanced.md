---
title: Usługa Azure Active Directory na podstawie grupy licencjonowania dodatkowe scenariusze | Dokumentacja firmy Microsoft
description: Więcej scenariusze dotyczące usługi Azure Active Directory na podstawie grupy licencji
services: active-directory
keywords: Licencjonowanie usługi Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: piotrci
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb8bd727618eda2a887cc9e1b739889204eb87fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764387"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenariusze, ograniczenia i znane problemy przy użyciu grup do zarządzania Licencjonowanie w usłudze Azure Active Directory

Aby uzyskać większą wiedzę na podstawie grupy licencjonowania usługi Azure Active Directory (Azure AD), należy użyć następujące informacje i przykłady.

## <a name="usage-location"></a>Lokalizacja użycia

Pewnych usług firmy Microsoft nie są dostępne we wszystkich lokalizacjach. Zanim można przypisać licencję do użytkownika, administrator musi określić **lokalizacji użytkowania** właściwości użytkownika. W [portalu Azure](https://portal.azure.com), można określić w **użytkownika** &gt; **profilu** &gt; **ustawienia**.

Przypisywaniu licencji grupy użytkowników bez użycia lokalizacji określonej będzie dziedziczyć lokalizację katalogu. Jeśli użytkownicy w wielu lokalizacjach, upewnij się odzwierciedlały która poprawnie obiektów użytkownika przed dodaniem użytkowników do grup licencji.

> [!NOTE]
> Przypisanie do grupy licencji nie zmodyfikuje istniejącej wartości lokalizacji użytkowania na koncie użytkownika. Zaleca się, że zawsze ustawiono lokalizacji użytkowania jako część programu przepływu tworzenia użytkownika w usłudze Azure AD (np. za pomocą AAD Connect) -, który zapewni wynik przypisania licencji zawsze jest poprawna, a użytkownicy nie będą odbierać usług w lokalizacjach, które nie są dozwolone.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Za pomocą licencjonowania na podstawie grupy z grupami dynamicznymi

Za pomocą licencjonowania na podstawie grupy z żadną inną grupą zabezpieczeń, co oznacza, że można łączyć z grupami dynamicznymi w usłudze Azure AD. Grup dynamicznych uruchamiane zasady dla użytkownika atrybutów obiektu, aby automatycznie dodawać i usuwać użytkowników z grup.

Na przykład można utworzyć grupę dynamicznych dla niektórych zestawu produktów, które chcesz przypisać do użytkowników. Każda grupa jest wypełniana przez regułę dodawania użytkowników według ich atrybutów, a każda grupa przypisano licencje, które chcesz otrzymywać. Można przypisać atrybutu lokalnej, a jego synchronizacji z usługą Azure AD, lub można zarządzać atrybutu bezpośrednio w chmurze.

Licencje są przypisane do użytkownika, wkrótce po dodaniu ich do grupy. Gdy ten atrybut zostanie zmieniona, użytkownik opuści grup i licencje zostaną usunięte.

### <a name="example"></a>Przykład

Rozważmy przykład rozwiązania zarządzania tożsamościami lokalnymi decyduje o tym, którzy użytkownicy powinni mieć dostęp do usług sieci web firmy Microsoft. Używa **extensionAttribute1** do przechowywania reprezentujący licencji, użytkownik musi mieć wartość ciągu. Azure AD Connect synchronizuje go z usługą Azure AD.

Użytkownicy mogą potrzebować jedną licencji, ale nie inna lub może być jednocześnie. Oto przykład, w którym jest rozpowszechniana Office 365 Enterprise E5 i Enterprise Mobility + Security (EMS) licencje do użytkowników w grupach:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: podstawowe usługi

![Zrzut ekranu z pakietu Office 365 Enterprise E5 podstawowej usługi](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licencjonowani użytkownicy

![Zrzut ekranu Enterprise Mobility + Security licencjonowani użytkownicy](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Na przykład modyfikowania jednego użytkownika i ustawiania ich extensionAttribute1 wartość `EMS;E5_baseservices;` Jeśli użytkownik ma mieć obu licencji. Istnieje możliwość modyfikacji lokalnymi. Po zmianie przeprowadza synchronizację z chmurą, użytkownik jest automatycznie dodawany do obu grup i przypisanych licencji.

![Zrzut ekranu pokazujący sposób ustawiania extensionAttribute1 użytkownika](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Modyfikowanie reguły członkostwa istniejącą grupę, należy zachować ostrożność. Po zmianie reguły członkostwa grupy zostanie oceniona i użytkowników, którzy nie są zgodne z regułą nowe będą usunięte (użytkowników, którzy nadal odpowiada nowej reguły nie zostaną zmienione w trakcie tego procesu). Ci użytkownicy będą licencje usunięte podczas procesu, co może spowodować utratę pracy lub w niektórych przypadkach utraty danych.

> Jeśli masz dużą grupę dynamiczne zależą przypisywaniu licencji, należy wziąć pod uwagę sprawdzanie poprawności żadnych większych zmian w mniejszych grupie testowej przed zastosowaniem ich do grupy głównego.

## <a name="multiple-groups-and-multiple-licenses"></a>Wiele grup i wielu licencji

Użytkownik może należeć do wielu grup z licencjami. Poniżej przedstawiono niektóre czynności, które należy uwzględnić:

- Wiele licencji dla tego samego produktu mogą nakładać się na i spowodować wszystkie włączone usługi są stosowane do użytkownika. W poniższym przykładzie przedstawiono dwie grupy licencji: *usługi podstawowej E3* zawiera usługi foundation, aby najpierw wdrożyć dla wszystkich użytkowników. I *E3 rozszerzony usług* zawiera dodatkowe usługi (kołysanie poprzeczne i planowania), aby wdrożyć tylko w przypadku niektórych użytkowników. W tym przykładzie użytkownik został dodany do obu grup:

  ![Zrzut ekranu przedstawiający włączone usługi](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  W związku z tym użytkownik ma 7 12 usług w produkcie włączona, podczas korzystania z tylko jedną licencję dla tego produktu.

- Wybieranie *E3* licencji zawiera więcej szczegółów, w tym informacje o tym, które grupy spowodował co usług być włączone dla użytkownika.

  ![Zrzut ekranu przedstawiający włączone usługi przez grupę](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Bezpośrednie licencji współistnieć z grupy licencji

Gdy użytkownik dziedziczy grupę licencji, nie można bezpośrednio usunąć ani zmodyfikować tego przypisania licencji w oknie właściwości użytkownika. Zmiany muszą wprowadzane w grupie, a następnie propagowany do wszystkich użytkowników.

Jest to możliwe, jednak można przypisać bezpośrednio do użytkownika, oprócz dziedziczone licencji tego samego licencji produktu. Dodatkowe usługi z produktu tylko dla jednego użytkownika, można włączyć bez wpływu na innych użytkowników.

Bezpośrednie przypisane licencje można je usunąć i nie mają wpływu na dziedziczone licencji. Należy wziąć pod uwagę użytkownika, który dziedziczy z grupy licencji usługi Office 365 Enterprise E3.

1. Początkowo użytkownik odziedziczył licencji tylko z *E3 podstawowe usługi* grupy, która umożliwia cztery planów usług, jak pokazano:

  ![Zrzut ekranu E3 włączona grupy usług](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Możesz wybrać **przypisać** bezpośrednio przypisać licencję E3 dla użytkownika. W takim przypadku użytkownik zamierza wyłączyć wszystkie plany usługi, z wyjątkiem usługi Yammer przedsiębiorstwa:

  ![Zrzut ekranu przedstawiający sposób przypisywania licencji bezpośrednio do użytkownika](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. W związku z tym użytkownik nadal używa tylko jedną licencję produktu E3. Ale usługi Yammer przedsiębiorstwa dla tego użytkownika tylko pozwala bezpośrednio przypisywać. Aby sprawdzić, usług, które są włączane przez członkostwo w grupie i bezpośredniego przypisania:

  ![Zrzut ekranu przedstawiający dziedziczone i bezpośredniego przypisania](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Gdy używasz bezpośrednio przypisywać dopuszczalne są następujące operacje:

  - Yammer przedsiębiorstwa można wyłączyć obiektu user bezpośrednio. **Włącz/Wyłącz** Przełącz na ilustracji został włączony dla tej usługi, w przeciwieństwie do innych przełączników usługi. Ponieważ usługa jest włączona bezpośrednio na użytkownika, może być modyfikowany.
  - Dodatkowe usługi można włączyć również jako część bezpośrednio przypisanej licencji.
  - **Usuń** przycisk może służyć do usunięcia bezpośredniego licencji użytkownika. Możesz sprawdzić, czy użytkownik tylko ma teraz dziedziczone grupy licencji i pozostać włączone, tylko oryginalny usługi:

    ![Zrzut ekranu pokazujący sposób usuwania przypisania bezpośredniego](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Zarządzanie usługami nowe dodane do produktów
Gdy Microsoft dodaje nową usługę do produktu, zostanie włączona domyślnie we wszystkich grupach, do których przypisano licencji produktu. Użytkowników w dzierżawie, którzy są subskrypcji powiadomień o zmianach produktu będą otrzymywać wiadomości e-mail z informacją o dodatków service nadchodzących wcześniejsze.

Jako administrator można przejrzeć wszystkie grupy, zmiany i podjąć działania, takie jak wyłączenie nową usługę w każdej grupie. Na przykład jeśli utworzono grup elementów docelowych tylko określone usługi do wdrożenia, możesz ponownie te grupy i upewnij się, że wszelkie nowo dodanych usługi zostały wyłączone.

Oto przykład co ten proces może wyglądać tak:

1. Pierwotnie przypisane *Office 365 Enterprise E5* produktu do wielu grup. Jeden z tych grup, nazywany *O365 E5 - tylko program Exchange* została zaprojektowana w celu umożliwienia tylko *usługi Exchange Online (Planowanie 2)* usługi dla jego elementów członkowskich.

2. Odebrano powiadomienie z firmy Microsoft, który produktu E5 zostanie rozszerzony o nową usługę - *Microsoft Stream*. Usługi staną się dostępne w Twojej dzierżawie, można wykonać następujące czynności:

3. Przejdź do [ **usługi Azure Active Directory > licencji > wszystkie produkty** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) bloku, a następnie wybierz *Office 365 Enterprise E5*, a następnie wybierz pozycję **grup licencji** Aby wyświetlić listę wszystkich grup z tego produktu.

4. Kliknij grupę, aby przejrzeć (w tym przypadku *O365 E5 - tylko program Exchange*). Spowoduje to otwarcie **licencji** kartę. Kliknięcie licencji E5 zostanie otwarty blok listę wszystkich usług włączone.
> [!NOTE]
> *Microsoft Stream* usługi został automatycznie dodawane i włączone w tej grupie oprócz *usługi Exchange Online* usługi:

  ![Zrzut ekranu przedstawiający nową usługę dodawane do grupy licencji](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Aby wyłączyć usługę nowe w tej grupie, kliknij przycisk **Włącz/Wyłącz** Przełącz obok usługi, a następnie kliknij przycisk **zapisać** przycisk, aby potwierdzić zmianę. Usługi Azure AD będzie teraz przetwarzać wszyscy użytkownicy w grupie można zastosować zmianę; nie ma żadnych nowych użytkowników do grupy *Microsoft Stream* włączona usługa.

  > [!NOTE]
  > Użytkownicy mogą nadal mieć usługi włączone przez niektóre inne przypisania licencji (innej grupy są członkami lub przypisania licencji bezpośredniego).

6. W razie potrzeby wykonaj te same kroki dla innych grup z tym produktem przypisane.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Aby zobaczyć, kto odziedziczył i bezpośredniego licencji za pomocą programu PowerShell
Skrypt programu PowerShell służy do sprawdzenia, czy użytkownik ma licencję przypisane bezpośrednio lub odziedziczone po grupie.

1. Uruchom `connect-msolservice` polecenia cmdlet do uwierzytelniania i Połącz z dzierżawą.

2. `Get-MsolAccountSku` może służyć do odnajdywania wszystkich licencji produktu elastycznie w dzierżawie.

  ![Zrzut ekranu przedstawiający polecenia cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Użyj *AccountSkuId* wartość licencję planuje się [ten skrypt programu PowerShell](./active-directory-licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Spowoduje to utworzenie listy użytkowników, którzy mają to licencja z informacje na temat sposobu jest przypisana licencja.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Umożliwia monitorowanie aktywności licencjonowania na podstawie grupy dzienników inspekcji

Można użyć [dzienników inspekcji usługi Azure AD](./active-directory-reporting-activity-audit-logs.md#audit-logs) do widzieć wszystkie działania związane z na podstawie grupy licencji, w tym:
- kto zmienił licencji na grupy
- podczas uruchamiania systemu przetwarzania zmian grupy licencji, a po jego zakończeniu
- wprowadzono zmiany licencji użytkownika wyniku przypisania licencji grupy.

>[!NOTE]
> Dzienniki inspekcji są dostępne w większości bloków w sekcji usługi Azure Active Directory w portalu. W zależności od tego, gdzie można uzyskiwać do nich dostęp filtry mogą być wstępnie stosowane tylko w celu wyświetlenia działanie odpowiednie w kontekście bloku. Jeśli nie widzisz wyniki spodziewasz się, sprawdź [opcje filtrowania](./active-directory-reporting-activity-audit-logs.md#filtering-audit-logs) lub uzyskiwanie dostępu do dzienników inspekcji niefiltrowane w obszarze [ **usługi Azure Active Directory > działania > Dzienniki inspekcji** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Sprawdzić, który zmodyfikował licencję grupy

1. Ustaw **działania** filtrowane w celu *zestaw grupy licencji* i kliknij przycisk **Zastosuj**.
2. Wyniki obejmują wszystkie przypadki licencji jest ustawiona lub modyfikować dla grupy.
>[!TIP]
> Możesz także wpisać nazwę grupy w *docelowej* filtr, aby określić zakres wyników.

3. Kliknij element w widoku listy, aby wyświetlić szczegóły co się zmieniło. W obszarze *zmodyfikowane właściwości* starych i nowych wartości dla przypisania licencji są wymienione.

Oto przykład ostatnie zmiany licencji grupy, ze szczegółami:

![Zrzut ekranu grupy licencji zmiany](media/active-directory-licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Dowiedzieć się, gdy zmiany w grupie rozpoczęcia i zakończenia przetwarzania

Po zmianie w grupie licencji usługi Azure AD rozpocznie zastosowania zmian do wszystkich użytkowników.

1. Aby zobaczyć, gdy grup zostało rozpoczęte przetwarzanie, ustaw **działania** filtrowane w celu *rozpocząć stosowanie grupy na podstawie licencji do użytkowników*. Należy zauważyć, że aktora operacji *usługi Microsoft Azure AD na podstawie grupy licencjonowania* -kontem systemowym, który służy do wykonywania wszystkich zmian w grupach licencji.
>[!TIP]
> Kliknij element na liście, aby wyświetlić *zmodyfikowane właściwości* pola — przedstawia zmiany licencji, które zostały pobrana do przetwarzania. Jest to przydatne, jeśli wprowadzono wiele zmian do grupy, a nie masz pewności, która została przetworzona.

2. Podobnie, aby zobaczyć, po zakończeniu przetwarzania w grupach, użyj wartości filtru *zastosowaniu na podstawie grupy licencji do użytkowników*.
>[!TIP]
> W takim przypadku *zmodyfikowane właściwości* pole zawiera podsumowanie wyników — jest to przydatne, można szybko sprawdzić, czy przetwarzanie spowodowało błędy. Przykładowe dane wyjściowe:
> ```
Modified Properties
...
Name : Result
Old Value : []
New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
> ```

3. Aby wyświetlić pełny dziennik dla jak grupy został przetworzony, w tym wszystkich zmian użytkownika, ustaw następujące filtry:
  - **Zainicjowane przez (aktora)**: "Microsoft Azure AD na podstawie grupy licencji"
  - **Zakres dat** (opcjonalnie): niestandardowy zakres gdy znasz określonej grupy rozpoczęcia i zakończenia przetwarzania

To przykładowe dane wyjściowe zawiera początku przetwarzania, wynikowe zmiany użytkowników i zakończenie przetwarzania.

![Zrzut ekranu grupy licencji zmiany](media/active-directory-licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kliknięcie pozycji powiązanych do *licencji użytkownika zmiany* wyświetli szczegóły dotyczące licencji zmiany zostały zastosowane do każdego użytkownika.

## <a name="deleting-a-group-with-an-assigned-license"></a>Usunięcie grupy z przypisaną licencję

Nie jest możliwe usuwanie grupy z licencją aktywne. Administrator może usunąć grupy nie realizująca, że spowoduje licencji były usuwane z użytkownicy — z tego powodu wymaganych przez nas najpierw należy usunąć z grupy, aby można było usunąć żadnych licencji.

Podczas próby usunięcia grupy w portalu Azure może zostać wyświetlony powiadomienie o błędzie podobny do tego: ![nie można usunąć grupy zrzut ekranu](media/active-directory-licensing-group-advanced/groupdeletionfailed.png)

Przejdź do **licencji** karcie w grupie i czy istnieją wszystkie przypisane licencje. Jeśli tak, należy usunąć te licencje i spróbuj ponownie usunąć grupy.

Podobne błędy mogą pojawić się podczas próby usunięcia grupy przy użyciu programu PowerShell lub interfejsu API programu Graph. Jeśli używane są synchronizowane z lokalnej grupy, Azure AD Connect również może raportować błędy, jeśli go nie powiodło się usunięcie grupy w usłudze Azure AD. W takich przypadkach upewnij się sprawdzić, czy wszystkie licencje przypisane do grupy i usuń je najpierw.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

Jeśli używasz licencjonowania na podstawie grupy, jest warto zapoznać się z poniższą listę ograniczenia i znane problemy.

- Na podstawie grupy licencjonowania aktualnie nie obsługuje grup, które zawierają inne (grup zagnieżdżonych). Licencji w przypadku zastosowania do grup zagnieżdżonych, tylko natychmiastowe pierwszego stopnia członkowie grupy mają licencje zastosowane.

- Funkcja może być używana tylko z grup zabezpieczeń. Office grup nie są obecnie obsługiwane i nie można używać ich w procesie przypisania licencji.

- [Portalu administracyjnego usługi Office 365](https://portal.office.com ) nie obsługuje obecnie na podstawie grupy licencji. Jeśli użytkownik dziedziczy grupę licencji, ta licencja zostanie wyświetlony w portalu administracyjnym pakietu Office jako licencji zwykłych użytkowników. Jeśli użytkownik próbuje zmodyfikować licencji lub spróbuj usunąć licencję, portalu zwraca komunikat o błędzie. Odziedziczone grupy licencji, nie można zmodyfikować bezpośrednio na koncie użytkownika.

- Jeśli użytkownik zostanie usunięty z grupy i traci licencji, planów usług z licencji (na przykład usługi SharePoint Online) są ustawione na **zawieszone** stanu. Plany usługi nie zostały skonfigurowane do stanu końcowego, wyłączony. W ten sposób można uniknąć przypadkowego usunięcia danych użytkownika, jeśli administrator wprowadza błędu w zarządzanie członkostwem grupy.

- Gdy licencje są przypisane lub modyfikować dla dużej grupy (na przykład 100 000 użytkowników), może ona wpływ na wydajność. W szczególności woluminu zmiany generowanych przez usługi Azure AD automatyzacji może niekorzystnie wpłynąć na wydajność usługi Azure AD Synchronizacja katalogu i systemami lokalnymi.

- W niektórych sytuacjach wysokie obciążenie przetwarzania licencji mogą być opóźnione i zmienia takich jak dodawanie/usuwanie grupy licencji lub dodawania/usuwania użytkowników z grupy, może zająć dużo czasu na przetworzenie. Zobaczysz zmiany zostaną więcej niż 24 godziny do przetwarzania, należy [Otwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) umożliwia firmie Microsoft w celu zbadania. Firma Microsoft poprawi charakterystyki wydajności tej funkcji, przed jego *ogólnej dostępności*.

- Automatyzacja zarządzania licencji nie reaguje automatycznie dla wszystkich typów zmian w środowisku. Na przykład możesz prawdopodobnie zabrakło licencji, co w przypadku niektórych użytkowników być w stanie błędu. Aby zwolnić miejsce na liczba dostępnych miejsc, można usunąć niektórych bezpośrednio przypisane licencje od innych użytkowników. Jednak system automatycznie reagowania na zmiany i Usuń użytkowników, w tym stanie błędu.

  Jako obejście tego ograniczenia typu, możesz przejść do **grupy** bloku w usłudze Azure AD i kliknij przycisk **ponownie przetworzyć**. To polecenie przetwarza wszystkich użytkowników w tej grupie i rozpoznaje stanów błąd, jeśli to możliwe.

- Na podstawie grupy licencji nie rejestruje błędy podczas licencji nie można przypisać do użytkownika z powodu konfiguracji proxy zduplikowany adres w usłudze Exchange Online; takie użytkownicy są pomijane podczas przypisywania licencji. Aby uzyskać więcej informacji na temat zidentyfikować i rozwiązać ten problem, zobacz [w tej sekcji](./active-directory-licensing-group-problem-resolution-azure-portal.md#license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o innych scenariuszy zarządzania licencji za pomocą licencjonowania na podstawie grupy, zobacz:

* [Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identyfikowanie i rozwiązywanie problemów z licencji dla grupy w usłudze Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Jak przeprowadzić migrację poszczególnych licencjonowanych użytkowników do licencjonowania oparte na grupach w usłudze Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
