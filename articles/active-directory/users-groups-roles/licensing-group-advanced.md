---
title: Oparte na grupach licencjonowania dodatkowe scenariusze — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Więcej scenariusze dotyczące licencjonowania opartego na grupach usługi Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24bf8e7cf103d583cf6604e0c529ad4ea267ce84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60471900"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenariusze, ograniczenia i znane problemy, używanie grup do zarządzania, Licencjonowanie w usłudze Azure Active Directory

Użyj następujących informacji i przykładów, aby uzyskać bardziej zaawansowanej wiedzy na temat licencjonowania opartego na grupach usługi Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Lokalizacja użycia

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić **lokalizacji użytkowania** właściwości użytkownika. W [witryny Azure portal](https://portal.azure.com), można określić w **użytkownika** &gt; **profilu** &gt; **ustawienia**.

W celu przypisania licencji grupy Wszyscy użytkownicy bez określonej lokalizacji użytkowania będzie dziedziczyć lokalizację katalogu. Jeśli masz użytkowników w wielu lokalizacjach, upewnij się to uwzględniała poprawnie w obiekty użytkownika przed dodaniem użytkowników do grup licencji.

> [!NOTE]
> Przypisanie licencji do grupy nigdy nie zmodyfikuje istniejącej wartości lokalizacji użycia na koncie użytkownika. Zaleca się, że zawsze ustawić lokalizację użytkowania jako część przepływu tworzenia użytkownika w usłudze Azure AD (np. przez program AAD Connect Konfiguracja) -, który zagwarantuje, że wynik przypisania licencji zawsze jest poprawna, a użytkownicy nie będą odbierać usług w lokalizacjach, które nie są dozwolone.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Użyj opartego na grupach licencji z grupami dynamicznymi

Można użyć, oparte na grupach licencji z żadną inną grupą zabezpieczeń, co oznacza, że można łączyć za pomocą grup dynamicznych usługi Azure AD. Grupy dynamiczne uruchamiać zasady użytkownika atrybutów obiektu, aby automatycznie dodawać i usuwać użytkowników z grup.

Na przykład można utworzyć grupę dynamiczną dla zestawu niektórych produktów, które chcesz przypisać do użytkowników. Każda grupa jest wypełniana przez regułę dodawania użytkowników według ich atrybutów, a każda grupa otrzymuje licencji, które chcesz otrzymywać. Można przypisać atrybutu w środowisku lokalnym i zsynchronizować ją z usługą Azure AD, ale można też zarządzać atrybut bezpośrednio w chmurze.

Licencje są przypisane do użytkownika, wkrótce, po dodaniu ich do grupy. Gdy ten atrybut zostanie zmieniona, użytkownik opuści grupy i licencje zostaną usunięte.

### <a name="example"></a>Przykład

Rozważmy przykład rozwiązanie zarządzania tożsamościami w środowisku lokalnym, który decyduje, którzy użytkownicy powinni mieć dostęp do usług sieci web firmy Microsoft. Używa ona **extensionAttribute1** do przechowywania wartości ciągu reprezentujący licencji użytkownik powinien mieć. Program Azure AD Connect synchronizuje je z usługą Azure AD.

Użytkownicy mogą potrzebować jednej licencji, ale nie do innego lub może być wymagane oba te cykle. Oto przykład, w którym jest rozpowszechniana Office 365 Enterprise E5 i rozwiązania Enterprise Mobility + Security (EMS) licencji do użytkowników w grupach:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: podstawowa usługi

![Zrzut ekranu programu Office 365 Enterprise E5 podstawowej usługi](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licencjonowani użytkownicy

![Zrzut ekranu Enterprise Mobility + Security — licencjonowani użytkownicy](./media/licensing-group-advanced/o365-e5-licensed-users.png)

W tym przykładzie należy zmodyfikować jednego użytkownika i ustaw ich extensionAttribute1 wartość `EMS;E5_baseservices;` Jeśli chcesz, aby użytkownik musi mieć obie licencje. Można wprowadzić tę zmianę w środowisku lokalnym. Po zmianie przeprowadza synchronizację z chmurą, użytkownik jest automatycznie dodawany do obu grup i przypisywania licencji.

![Zrzut ekranu przedstawiający sposób ustawiania extensionAttribute1 użytkownika](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Należy zachować ostrożność podczas modyfikowania istniejącej grupy reguły członkostwa. Po zmianie reguły członkostwa w grupie będą ponownie oceniane i użytkowników, którzy nie są już zgodne Nowa reguła będzie usunięty (użytkowników, którzy nadal dopasowanie, nie będzie mieć wpływ na nową regułę w trakcie tego procesu). Ci użytkownicy będą licencje usunięte podczas procesu, co może spowodować utratę usługi lub w niektórych przypadkach utraty danych.
> 
> W przypadku dużej grupy dynamiczne, zależeć od w celu przypisania licencji należy wziąć pod uwagę przed zastosowaniem ich do grupy głównego sprawdzania poprawności żadnych większych zmian na mniejsze grupy testowej.

## <a name="multiple-groups-and-multiple-licenses"></a>Wiele grup i wiele licencji

Użytkownik może należeć do wielu grup z licencjami. Poniżej przedstawiono niektóre kwestie, należy wziąć pod uwagę:

- Wiele licencji dla tego samego produktu może pokrywać się i powodują one wszystkie włączone usługi, które są stosowane do użytkownika. Poniższy przykład przedstawia dwa grup licencji: *Usługi podstawowej E3* zawiera usługi foundation, aby najpierw wdrożyć dla wszystkich użytkowników. I *E3 rozszerzone usług* zawiera dodatkowe usługi (aplikacji Sway i Planner), aby wdrożyć tylko dla niektórych użytkowników. W tym przykładzie użytkownik został dodany do obu grup:

  ![Zrzut ekranu przedstawiający włączone usługi](./media/licensing-group-advanced/view-enabled-services.png)

  W rezultacie użytkownik ma 7 12 usług w produkcie włączone podczas korzystania z tylko jedną licencję dla tego produktu.

- Wybieranie *E3* licencji zawiera bardziej szczegółowe informacje, w tym informacje o tym, które spowodowało grup, jakich usług mają być włączone dla użytkownika.

  ![Zrzut ekranu przedstawiający włączone usługi przez grupę](./media/licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Bezpośrednie licencji współistnieć z licencjami grup

Po użytkownik dziedziczy grupę licencji, nie można bezpośrednio usunąć ani zmodyfikować tego przypisania licencji, w oknie właściwości użytkownika. Zmiany musi być wprowadzona w grupie i następnie propagowany do wszystkich użytkowników.

Jest to możliwe, jednak można przypisać tego samego licencję na produkt bezpośrednio do użytkownika, oprócz odziedziczonej licencji. Dodatkowe usługi z produktu tylko dla jednego użytkownika, można włączyć bez wywierania wpływu na innych użytkowników.

Bezpośrednio przypisane licencje można je usunąć, a nie mają wpływu na dziedziczone licencji. Należy wziąć pod uwagę użytkownika, który dziedziczy licencję usługi Office 365 Enterprise E3 z grupy.

1. Początkowo użytkownik odziedziczył licencji tylko z *E3 podstawowych usług* grupy, która umożliwia czterech planów usług, jak pokazano:

   ![Zrzut ekranu E3 usług obsługujących grupy](./media/licensing-group-advanced/e3-group-enabled-services.png)

2. Możesz wybrać **przypisać** bezpośrednio przypisać licencję usługi E3 dla użytkownika. W tym przypadku ma wyłączenie wszystkich planach usługi, z wyjątkiem usługi Yammer przedsiębiorstwa:

   ![Zrzut ekranu przedstawiający sposób bezpośrednio przypisać licencję użytkownika](./media/licensing-group-advanced/assign-license-to-user.png)

3. W rezultacie użytkownik nadal używa tylko jedną licencję produktu E3. Ale przypisania bezpośredniego włącza usługę Yammer przedsiębiorstwa dla tego użytkownika tylko. Aby zobaczyć, które usługi są włączane przez członkostwo w grupie i bezpośrednie przypisanie:

   ![Zrzut ekranu przedstawiający dziedziczone i przypisania bezpośredniego](./media/licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Gdy używasz przypisania bezpośredniego, dopuszczalne są następujące operacje:

   - Przedsiębiorstwa w usłudze Yammer można wyłączyć obiektu user bezpośrednio. **Włączenia/wyłączenia** Przełącz na ilustracji został włączony dla tej usługi, w przeciwieństwie do innych przełącza usługi. Ponieważ usługa jest włączone bezpośrednio na użytkownika, można go modyfikować.
   - Dodatkowe usługi można włączyć także jako część bezpośrednio przypisanej licencji.
   - **Usuń** przycisk służy do usuwania bezpośrednie licencji użytkownika. Widać, że użytkownik tylko ma teraz licencji grupy dziedziczone i pozostać włączone, tylko oryginalnej usługi:

     ![Zrzut ekranu przedstawiający sposób usuwania przypisania bezpośredniego](./media/licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Zarządzanie nowymi usługami dodany do produktów
Gdy Microsoft dodaje nową usługę do produktu, zostanie ono zostać włączone domyślnie we wszystkich grupach, do których przypisano licencję na produkt. Użytkownicy w Twojej dzierżawie, którzy mają subskrypcję powiadomienia o zmianach wprowadzonych w produkcie będą otrzymywać wiadomości e-mail informacją o dodatkach usługi, która wcześniej.

Jako administrator możesz przejrzeć wszystkie grupy wpływ zmiany i podejmować działania, takie jak wyłączenie nowej usługi w każdej grupie. Na przykład jeśli utworzono grup przeznaczonych dla tylko określone usługi do wdrożenia, możesz ponownie te grupy i upewnij się, że dowolne nowo dodanych usługi zostały wyłączone.

Oto przykład tego procesu może wyglądać:

1. Początkowo przypisana *Office 365 Enterprise E5* produktu do kilku grup. Jedna z tych grup o nazwie *E5 usługi Office 365 — tylko program Exchange* zaprojektowano tak, aby włączyć tylko *usługi Exchange Online (Plan 2)* usługi, aby jej członków.

2. Odebrano powiadomienie firmy Microsoft, która produktu E5 będzie można rozszerzyć za pomocą nowego usługi - *Microsoft Stream*. Gdy usługa stanie się dostępne w Twojej dzierżawie, można wykonać następujące czynności:

3. Przejdź do [ **usługi Azure Active Directory > licencji > wszystkie produkty** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) bloku, a następnie wybierz pozycję *Office 365 Enterprise E5*, a następnie wybierz **licencjonowane grupy** Aby wyświetlić listę wszystkich grup z danego produktu.

4. Kliknij grupę, aby przejrzeć (w tym przypadku *E5 usługi Office 365 — tylko program Exchange*). Spowoduje to otwarcie **licencji** kartę. Kliknięcie na licencji E5 spowoduje otwarcie bloku z listą wszystkich włączonych usług.
   > [!NOTE]
   > *Microsoft Stream* usługi została automatycznie dodana i włączone w tej grupie, oprócz *usługi Exchange Online* usługi:

   ![Zrzut ekranu przedstawiający nowe usługi dodawane do grupy licencji](./media/licensing-group-advanced/manage-new-services.png)

5. Jeśli chcesz wyłączyć nowej usługi w tej grupie, kliknij przycisk **włączenia/wyłączenia** Przełącz obok usługi, a następnie kliknij przycisk **Zapisz** przycisk, aby potwierdzić zmianę. Usługa Azure AD będzie teraz przetwarzać wszyscy użytkownicy w grupie, aby zastosować zmianę; nie ma żadnych nowych użytkowników do grupy *Microsoft Stream* włączona usługa.

   > [!NOTE]
   > Użytkownicy mogą nadal mieć service włączone za pomocą niektórych innych przypisanie licencji (innej grupy elementów członkowskich i bezpośrednie przypisanie licencji).

6. Jeśli to konieczne, wykonaj te same kroki dla innych grup, z tym produktem przypisane.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Aby zobaczyć, kto odziedziczył i bezpośredniego licencji przy użyciu programu PowerShell
Skrypt programu PowerShell służy do sprawdzenia, czy użytkownicy mają licencję przypisane bezpośrednio lub odziedziczone po grupie.

1. Uruchom `connect-msolservice` polecenia cmdlet w celu uwierzytelnienia i nawiązania połączenia z dzierżawą.

2. `Get-MsolAccountSku` może służyć do odnajdywania wszystkich licencji produktu aprowizowane w dzierżawie.

   ![Zrzut ekranu przedstawiający polecenia cmdlet Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Użyj *AccountSkuId* wartość licencji interesuje Cię przy użyciu [ten skrypt programu PowerShell](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Generuje listę użytkowników, którzy mają niniejszej licencji, informacje na temat sposobu ma przypisaną licencję.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Korzystaj z dzienników inspekcji do monitorowania aktywności licencjonowania opartego na grupach

Możesz użyć [dzienniki inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) Aby wyświetlić wszystkie działania związane oparte na grupach licencji, w tym:
- kto go zmienił licencje na grupy
- podczas uruchamiania systemu, przetwarzanie zmiany licencji grupy, a po jej zakończeniu
- wprowadzono zmiany licencji użytkownika wyniku przypisania licencji grupy.

>[!NOTE]
> Dzienniki inspekcji są dostępne w większości bloki w sekcji usługi Azure Active Directory w portalu. W zależności od tego, gdzie możesz uzyskiwać do nich dostęp filtry mogą być wstępnie stosowane tylko w celu wyświetlenia działanie odpowiednie do kontekstu bloku. Jeśli nie widzisz oczekiwanych wyników, sprawdź [opcje filtrowania](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) ani uzyskać dostępu do dzienników inspekcji niefiltrowane w obszarze [ **usługi Azure Active Directory > działanie > Dzienniki inspekcji** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Dowiedz się, który zmodyfikował licencję grupy

1. Ustaw **działania** filtr, aby *Ustawianie licencji grupy* i kliknij przycisk **Zastosuj**.
2. Wyniki obejmują wszystkie przypadki licencji jest ustawiona lub modyfikacji grup.
   >[!TIP]
   > Można także wpisać nazwę grupy w *docelowej* filtr, aby określić zakres wyników.

3. Kliknij element w widoku listy, aby wyświetlić szczegóły co zmieniło się. W obszarze *zmodyfikowane właściwości* starych i nowych wartości w celu przypisania licencji są wymienione.

Oto przykład ostatnie zmiany licencji grupy, szczegóły:

![Zmiany licencji grupy zrzut ekranu](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Dowiedz się, gdy zmiany w grupie rozpoczęcia i zakończenia przetwarzania

Po zmianie w grupie licencji usługi Azure AD rozpocznie stosowania zmian do wszystkich użytkowników.

1. Aby wyświetlić uruchomienia przetwarzania grupy, ustaw **działania** filtr, aby *Rozpocznij stosowanie licencji opartej na grupie użytkowników*. Należy zauważyć, że aktora dla operacji *usługi Microsoft Azure AD Licencjonowanie oparte na grupach* — konto systemowe, które służy do wykonywania wszystkich zmian do grup licencji.
   >[!TIP]
   > Kliknij element listy w celu wyświetlenia *zmodyfikowane właściwości* pole — pokazuje zmiany licencji, które zostały wybrany do przetwarzania. Jest to przydatne, jeśli wprowadzono wiele zmian do grupy, a nie masz pewności, który z nich został przetworzony.

2. Podobnie, aby zobaczyć, gdy przetwarzanie zostało zakończone, grupy, użyj wartości filtru *Kończenie stosowania licencji opartej na grupie użytkowników*.
   > [!TIP]
   > W tym przypadku *zmodyfikowane właściwości* pole zawiera podsumowanie wyników — dzięki takiemu grupowaniu można szybko sprawdzić, jeśli przetwarzanie spowodowało wszelkie błędy. Przykładowe dane wyjściowe:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Aby wyświetlić pełny dziennik dla jak grupy został przetworzony, w tym wszystkie zmiany użytkownika, ustaw następujące filtry:
   - **Zainicjowane przez (Aktor)**: "Licencjonowanie oparte na grupach usługi Microsoft Azure AD"
   - **Zakres dat** (opcjonalnie): niestandardowy zakres o których wiadomo, konkretną grupę rozpoczęcia i zakończenia przetwarzania

To przykładowe dane wyjściowe pokazują rozpoczęcia przetwarzania, wynikowe zmiany użytkowników i wykończenia przetwarzania.

![Zmiany licencji grupy zrzut ekranu](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Klikając elementy związane z *Zmień licencję użytkownika* wyświetli szczegóły dotyczące licencji zmiany zostały wprowadzone do każdego użytkownika.

## <a name="deleting-a-group-with-an-assigned-license"></a>Usuwanie grupy z przypisanej licencji

Nie jest możliwe usunąć grupę za pomocą active przypisaną licencję usługi. Administrator może usunąć grupy nie zawierającemu, że spowoduje licencji były usuwane z użytkowników — z tego powodu, wymagamy, aby wszystkie licencje, aby najpierw usunąć z grupy, aby można było usunąć.

Podczas próby usunięcia grupy w witrynie Azure portal może zostać wyświetlony powiadomienia o błędzie następująco: ![Nie można usunąć grupy zrzut ekranu](./media/licensing-group-advanced/groupdeletionfailed.png)

Przejdź do **licencji** karty w grupie i sprawdzić, czy są wszystkie licencje przypisane. Jeśli tak, usuń te licencje i spróbuj ponownie usunąć grupy.

Podobne błędy mogą pojawić się podczas próby usunięcia grupy za pomocą programu PowerShell lub interfejsu API programu Graph. Jeśli używane są synchronizowane z lokalnej grupy, program Azure AD Connect może także raportowania błędów, jeśli go nie powiodło się usunięcie grupy w usłudze Azure AD. W takich przypadkach upewnij się sprawdzić, czy istnieją inne licencje przypisane do grupy i usuń je najpierw.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

Jeśli używasz licencjonowania opartego na grupach, to dobry pomysł, aby zapoznać się z poniższej listy ograniczenia i znane problemy.

- Licencjonowanie na podstawie grupy aktualnie nie obsługuje grup, które zawierają inne (grup zagnieżdżonych). Jeśli zastosujesz licencję do grupy zagnieżdżonej, zostanie ona przypisana tylko do użytkowników będących bezpośrednimi członkami pierwszego poziomu grupy.

- Tej funkcji należy używać tylko z grup zabezpieczeń i grup usługi Office 365, które mają securityEnabled = TRUE.

- [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com) nie obsługuje obecnie Licencjonowanie na podstawie grupy. Jeśli użytkownik dziedziczy grupę licencji, tej licencji pojawia się w portalu administracyjnym usługi Office jako licencji zwykłych użytkowników. Jeśli spróbujesz zmodyfikować tej licencji lub spróbuj usunąć licencję portal zwróci komunikat o błędzie. Odziedziczone grupy licencji, nie można zmodyfikować bezpośrednio na koncie użytkownika.

- Gdy licencje są przypisane lub modyfikowane dużej grupy (np. 100 000 użytkowników), może to wpłynąć na wydajność. W szczególności ilość zmian wygenerowanych przez automatyzację usługi Azure AD może niekorzystnie wpłynąć na wydajność usługi synchronizacji katalogu między usługami Azure AD i systemach lokalnych.

- Jeśli używasz grup dynamicznych do zarządzania członkostwem użytkowników, sprawdź, czy dany użytkownik należy do grupy. Jest to wymagane do przypisania licencji. Jeśli tak nie jest, [sprawdź stan przetwarzania reguły członkostwa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) grupy dynamicznej. 

- W niektórych sytuacjach dużym obciążeniem go może potrwać dłuższy czas przetwarzania zmian licencji dla grupy lub zmiany członkostwa w grupach, przy użyciu istniejących licencji. Zostanie wyświetlony zmiany zostaną dłużej niż 24 godziny do przetwarzania, grupy użytkowników 60K rozmiar lub mniej, [Otwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) zezwala na badania. 

- Automatyzacja zarządzania licencji nie reaguje automatycznie dla wszystkich typów zmian w środowisku. Na przykład należy prawdopodobnie zabrakło licencji, powodując niektórych użytkowników być w stanie błędu. Aby zwolnić miejsce na liczba dostępnych miejsc, można usunąć niektórych bezpośrednio przypisanymi licencjami od innych użytkowników. Jednak system nie reagować na tę zmianę i automatycznie rozwiązać użytkowników w tym stanie błędu.

  Jako obejście tego rodzaju ograniczenia, możesz przejść do **grupy** bloku w usłudze Azure AD i kliknij przycisk **ponownie przetworzyć**. To polecenie przetwarza wszyscy użytkownicy w tej grupie i jest rozpoznawana jako stany błędu, jeśli jest to możliwe.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencjami w ramach programu licencjonowania opartego na grupach, zobacz:

* [Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak przeprowadzić migrację użytkowników między licencjami produktów za pomocą licencjonowania opartego na grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Przykłady programu PowerShell dla licencjonowania opartego na grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
