---
title: Dodatkowe scenariusze licencjonowania oparte na grupach — Azure AD | Dokumenty firmy Microsoft
description: Więcej scenariuszy licencjonowania opartego na grupach usługi Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139d7e0cf2b57cc466dc97370b90a599257ce755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266288"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenariusze, ograniczenia i znane problemy przy użyciu grup do zarządzania licencjonowaniem w usłudze Azure Active Directory

Skorzystaj z poniższych informacji i przykładów, aby uzyskać bardziej zaawansowaną wiedzę na temat licencjonowania opartego na grupie usługi Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Lokalizacja użycia

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Zanim licencja może być przypisana do użytkownika, administrator musi określić **właściwość Lokalizacja użycia** użytkownika. W [witrynie Azure portal](https://portal.azure.com)można określić lokalizację użycia w **ustawieniach** **profilu** &gt; **użytkownika** &gt; .

W przypadku przypisania licencji grupowej użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu. Jeśli masz użytkowników w wielu lokalizacjach, przed dodaniem użytkowników z licencjami należy to poprawnie odzwierciedlić w zasobach użytkowników.

> [!NOTE]
> Przypisanie licencji grupy nigdy nie zmodyfikuje istniejącej wartości lokalizacji użycia użytkownika. Zaleca się, aby zawsze ustawić lokalizację użycia jako część przepływu tworzenia użytkownika w usłudze Azure AD (np. za pośrednictwem konfiguracji AAD Connect) — co zapewni, że wynik przypisania licencji jest zawsze poprawny, a użytkownicy nie otrzymują usług w lokalizacjach, które nie są dozwolone.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Korzystanie z licencjonowania opartego na grupach z grupami dynamicznymi

Licencjonowanie oparte na grupach można używać z dowolną grupą zabezpieczeń, co oznacza, że można je łączyć z grupami dynamicznymi usługi Azure AD. Grupy dynamiczne uruchamiają reguły względem atrybutów zasobów użytkownika, aby automatycznie dodawać i usuwać użytkowników z grup.

Na przykład można utworzyć grupę dynamiczną dla niektórych zestaw produktów, które mają być przypisane do użytkowników. Każda grupa jest wypełniana przez regułę dodawania użytkowników według ich atrybutów, a każdej grupie są przypisywane licencje, które mają otrzymać. Można przypisać atrybut lokalnie i zsynchronizować go z usługą Azure AD lub można zarządzać atrybutem bezpośrednio w chmurze.

Licencje są przypisywane do użytkownika wkrótce po dodaniu ich do grupy. Po zmianie atrybutu użytkownik opuszcza grupy, a licencje są usuwane.

### <a name="example"></a>Przykład

Rozważmy przykład lokalnego rozwiązania do zarządzania tożsamościami, które decyduje, którzy użytkownicy powinni mieć dostęp do usług sieci Web firmy Microsoft. Używa **extensionAttribute1** do przechowywania wartości ciągu reprezentującego licencje, które użytkownik powinien mieć. Usługa Azure AD Connect synchronizuje ją z usługą Azure AD.

Użytkownicy mogą potrzebować jednej licencji, ale nie innej, lub mogą potrzebować obu. Oto przykład, w którym licencje usługi Office 365 Enterprise E5 i Enterprise Mobility + Security (EMS) są dystrybuowane do użytkowników w grupach:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: usługi podstawowe

![Zrzut ekranu przedstawiający usługi podstawowe usługi Office 365 Enterprise E5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licencjonowani użytkownicy

![Zrzut ekranu przedstawiający użytkowników licencjonowanych w ramach programu Enterprise Mobility + Security](./media/licensing-group-advanced/o365-e5-licensed-users.png)

W tym przykładzie zmodyfikuj jednego użytkownika i `EMS;E5_baseservices;` ustaw jego rozszerzenieAttribute1 na wartość, jeśli chcesz, aby użytkownik miał obie licencje. Tę modyfikację można wprowadzić lokalnie. Po zsynchronizowania zmiany z chmurą użytkownik jest automatycznie dodawany do obu grup, a licencje są przypisywane.

![Zrzut ekranu przedstawiający sposób ustawiania rozszerzenia użytkownikaAttribute1](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Podczas modyfikowania reguły członkostwa istniejącej grupy należy zachować ostrożność. Po zmianie reguły członkostwo w grupie zostanie ponownie ocenione, a użytkownicy, którzy nie będą już pasować do nowej reguły, zostaną usunięci (użytkownicy, którzy nadal pasują do nowej reguły, nie zostaną dotknięci w trakcie tego procesu). Użytkownicy ci zostaną usunięci podczas procesu, co może spowodować utratę usługi lub w niektórych przypadkach utratę danych.
> 
> Jeśli masz dużą grupę dynamiczną, od której zależy przypisanie licencji, rozważ sprawdzenie poprawności wszelkich istotnych zmian w mniejszej grupie testowej przed zastosowaniem ich do grupy głównej.

## <a name="multiple-groups-and-multiple-licenses"></a>Wiele grup i wiele licencji

Użytkownik może być członkiem wielu grup z licencjami. Oto kilka rzeczy, które należy wziąć pod uwagę:

- Wiele licencji dla tego samego produktu może się nakładać i powodują, że wszystkie włączone usługi są stosowane do użytkownika. W poniższym przykładzie przedstawiono dwie grupy licencjonowania: *Usługi podstawowe E3* zawiera usługi fundacji do wdrożenia w pierwszej kolejności dla wszystkich użytkowników. Rozszerzone *usługi E3* zawierają dodatkowe usługi (Sway i Planner) do wdrożenia tylko dla niektórych użytkowników. W tym przykładzie użytkownik został dodany do obu grup:

  ![Zrzut ekranu przedstawiający włączone usługi](./media/licensing-group-advanced/view-enabled-services.png)

  W rezultacie użytkownik ma 7 z 12 usług w produkcie włączone, podczas korzystania tylko z jednej licencji dla tego produktu.

- Wybranie licencji *E3* zawiera więcej szczegółów, w tym informacje o tym, które usługi są włączone dla użytkownika przez przypisanie licencji grupy.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Licencje bezpośrednie współistnieją z licencjami grupowymi

Gdy użytkownik dziedziczy licencję z grupy, nie można bezpośrednio usunąć lub zmodyfikować tego przypisania licencji we właściwościach użytkownika. Zmiany muszą być wprowadzone w grupie, a następnie propagowane do wszystkich użytkowników.

Istnieje jednak możliwość przypisania tej samej licencji produktu bezpośrednio do użytkownika, oprócz licencji dziedziczonej. Możesz włączyć dodatkowe usługi z produktu tylko dla jednego użytkownika, bez wpływu na innych użytkowników.

Bezpośrednio przypisane licencje mogą zostać usunięte i nie mają wpływu na licencje dziedziczone. Należy wziąć pod uwagę użytkownika, który dziedziczy licencję usługi Office 365 Enterprise E3 z grupy.

Początkowo użytkownik dziedziczy licencję tylko z grupy *usług podstawowych E3,* która umożliwia cztery plany usług.

1. Wybierz **pozycję Przypisz,** aby bezpośrednio przypisać licencję E3 do użytkownika. W takim przypadku zamierzasz wyłączyć wszystkie plany usług z wyjątkiem usługi Yammer Enterprise.

    W rezultacie użytkownik nadal używa tylko jednej licencji produktu E3. Jednak przypisanie bezpośrednie umożliwia usługę Yammer Enterprise tylko dla tego użytkownika. Możesz zobaczyć, które usługi są włączone przez członkostwo w grupie w porównaniu z przypisaniem bezpośrednim.

1. Podczas korzystania z przypisania bezpośredniego dozwolone są następujące operacje:

   - Usługę Yammer Enterprise można bezpośrednio wyłączyć w zasobie użytkownika. **Przełącznik On/Off na** ilustracji został włączony dla tej usługi, w przeciwieństwie do innych przełączników usługi. Ponieważ usługa jest włączona bezpośrednio na użytkownika, można ją zmodyfikować.
   - Dodatkowe usługi mogą być również włączone, jako część bezpośrednio przypisanej licencji.
   - Przycisk **Usuń** może służyć do usunięcia licencji bezpośredniej od użytkownika. Widać, że użytkownik ma teraz tylko licencję grupy dziedziczonej i tylko oryginalne usługi pozostają włączone:

## <a name="managing-new-services-added-to-products"></a>Zarządzanie nowymi usługami dodawanymi do produktów

Gdy firma Microsoft doda nową usługę do planu licencji produktu, jest domyślnie włączona we wszystkich grupach, do których przypisano licencję produktu. Użytkownicy w dzierżawie, którzy subskrybują powiadomienia o zmianach produktu, otrzymają e-maile z wyprzedzeniem, powiadamiając ich o nadchodzących dodatkach do usługi.

Jako administrator możesz przejrzeć wszystkie grupy, których dotyczy zmiana, i podjąć działania, takie jak wyłączenie nowej usługi w każdej grupie. Na przykład jeśli utworzono grupy przeznaczone tylko dla określonych usług dla wdrożenia, można ponownie odwiedzić te grupy i upewnij się, że wszystkie nowo dodane usługi są wyłączone.

Oto przykład tego, jak może wyglądać ten proces:

1. Pierwotnie produkt *Office 365 Enterprise E5* został przypisany do kilku grup. Jedna z tych grup o nazwie *O365 E5 — tylko program Exchange* został zaprojektowany, aby włączyć tylko usługę Exchange Online *(Plan 2)* dla swoich członków.

2. Otrzymałeś powiadomienie od firmy Microsoft, że produkt E5 zostanie rozszerzony o nową usługę - *Microsoft Stream*. Gdy usługa staje się dostępna w dzierżawie, można wykonać następujące czynności:

3. Przejdź do [**bloku Licencje > usługi Azure Active Directory > Wszystkie produkty**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) i wybierz pozycję Office *365 Enterprise E5*, a następnie wybierz **pozycję Grupy licencjonowane,** aby wyświetlić listę wszystkich grup z tym produktem.

4. Kliknij grupę, którą chcesz przejrzeć (w tym przypadku *O365 E5 - Tylko wymiana).* Spowoduje to otwarcie karty **Licencje. Kliknięcie** licencji E5 spowoduje otwarcie bloku zawierającego listę wszystkich włączonych usług.
   > [!NOTE]
   > Usługa *Microsoft Stream* została automatycznie dodana i włączona w tej grupie, oprócz usługi Exchange *Online:*

   ![Zrzut ekranu przedstawiający nową usługę dodaną do licencji grupy](./media/licensing-group-advanced/manage-new-services.png)

5. Jeśli chcesz wyłączyć nową usługę w tej grupie, kliknij przełącznik **Włączanie/Wyłączanie** obok usługi i kliknij przycisk **Zapisz,** aby potwierdzić zmianę. Usługa Azure AD będzie teraz przetwarzać wszystkich użytkowników w grupie, aby zastosować zmianę; nowi użytkownicy dodani do grupy nie będą mieli włączonej usługi *Microsoft Stream.*

   > [!NOTE]
   > Użytkownicy mogą nadal mieć włączoną usługę za pośrednictwem innego przypisania licencji (innej grupy, do których są członkami lub bezpośredniego przypisania licencji).

6. W razie potrzeby wykonaj te same czynności dla innych grup z przypisanym produktem.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Użyj programu PowerShell, aby zobaczyć, kto odziedziczył i bezpośrednie licencje
Skrypt programu PowerShell służy do sprawdzania, czy użytkownicy mają licencję przypisaną bezpośrednio lub dziedziczoną z grupy.

1. Uruchom `connect-msolservice` polecenie cmdlet, aby uwierzytelnić i połączyć się z dzierżawą.

2. `Get-MsolAccountSku`może służyć do odnajdowania wszystkich aprowizowanych licencji produktu w dzierżawie.

   ![Zrzut ekranu przedstawiający polecenie cmdlet Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Użyj wartości *AccountSkuId* dla licencji, która Cię interesuje za pomocą [tego skryptu programu PowerShell](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Spowoduje to wyświetlenia listy użytkowników, którzy mają tę licencję z informacjami o tym, jak licencja jest przypisana.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Monitorowanie działań licencjonowania opartych na grupach za pomocą dzienników inspekcji

Za pomocą [dzienników inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) można wyświetlić wszystkie działania związane z licencjonowaniem opartym na grupach, w tym:
- który zmienił licencje w grupach
- kiedy system rozpoczął przetwarzanie zmiany licencji grupowej i po jej zakończeniu
- jakie zmiany licencji zostały wprowadzone do użytkownika w wyniku przypisania licencji grupy.

>[!NOTE]
> Dzienniki inspekcji są dostępne w większości bloków w sekcji Usługi Azure Active Directory w portalu. W zależności od tego, gdzie można uzyskać do nich dostęp, filtry mogą być wstępnie stosowane tylko pokazać działanie istotne dla kontekstu bloku. Jeśli nie widzisz oczekiwanych wyników, sprawdź [opcje filtrowania](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) lub uzyskaj dostęp do niefiltrowanych dzienników inspekcji w obszarze [**Dzienniki inspekcji > działania usługi Azure Active Directory > inspekcji**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Dowiedz się, kto zmodyfikował licencję grupową

1. Ustaw filtr **Aktywność,** aby *ustawić licencję grupy* i kliknij przycisk **Zastosuj**.
2. Wyniki obejmują wszystkie przypadki umieszczania lub modyfikowanie licencji w grupach.
   >[!TIP]
   > Można również wpisać nazwę grupy w filtrze *docelowym,* aby zakres wyników.

3. Wybierz element na liście, aby zobaczyć szczegóły tego, co się zmieniło. W obszarze *Zmodyfikowane właściwości* są wyświetlane zarówno stare, jak i nowe wartości przypisania licencji.

Oto przykład ostatnich zmian licencji grupowych ze szczegółami:

![Zmiany licencji grupy zrzutu ekranu](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Dowiedz się, kiedy rozpoczęto i zakończono przetwarzanie zmian w grupie

Gdy licencja ulegnie zmianie w grupie, usługa Azure AD rozpocznie stosowanie zmian do wszystkich użytkowników.

1. Aby zobaczyć, kiedy grupy rozpoczęły przetwarzanie, ustaw filtr **Aktywność** na *Rozpocznij stosowanie licencji opartej na grupach dla użytkowników*. Należy zauważyć, że podmiotem operacji jest *licencjonowanie oparte na grupie usługi Microsoft Azure AD* — konto systemowe, które jest używane do wykonywania wszystkich zmian licencji grupy.
   >[!TIP]
   > Kliknij element na liście, aby wyświetlić pole *Zmodyfikowane właściwości* — pokazuje zmiany licencji, które zostały pobrane do przetworzenia. Jest to przydatne w przypadku wielu zmian w grupie i nie masz pewności, która z nich została przetworzona.

2. Podobnie, aby zobaczyć, kiedy grupy zakończyły przetwarzanie, użyj wartości filtru *Zakończ stosowanie licencji opartej na grupie dla użytkowników*.
   > [!TIP]
   > W takim przypadku pole *Zmodyfikowane właściwości* zawiera podsumowanie wyników — jest to przydatne do szybkiego sprawdzenia, czy przetwarzanie spowodowało błędy. Przykładowe dane wyjściowe:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Aby wyświetlić pełny dziennik sposobu przetwarzania grupy, w tym wszystkie zmiany użytkownika, ustaw następujące filtry:
   - **Zainicjowane przez (aktora)**: "Licencjonowanie oparte na grupach usług Microsoft Azure AD"
   - **Zakres dat** (opcjonalnie): niestandardowy zakres, gdy znasz określoną grupę rozpoczętą i zakończono przetwarzanie

To przykładowe dane wyjściowe pokazuje początek przetwarzania, wszystkie wynikające zmiany użytkownika i zakończenia przetwarzania.

![Zmiany licencji grupy zrzutu ekranu](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Kliknięcie pozycji związanych z *poleceniem Zmień licencję użytkownika* spowoduje wyświetlenie szczegółów dotyczących zmian licencji zastosowanych do każdego użytkownika.

## <a name="deleting-a-group-with-an-assigned-license"></a>Usuwanie grupy z przypisaną licencją

Nie można usunąć grupy z przypisaną aktywną licencją. Administrator może usunąć grupę, która nie zdawała sobie sprawy, że spowoduje to usunięcie licencji z użytkowników — z tego powodu wymagamy usunięcia wszelkich licencji z grupy, zanim będzie można je usunąć.

Podczas próby usunięcia grupy w witrynie Azure portal może ![pojawić się powiadomienie o błędzie w ten sposób: Usunięcie grupy zrzutu ekranu nie powiodło się](./media/licensing-group-advanced/groupdeletionfailed.png)

Przejdź do karty **Licencje** w grupie i sprawdź, czy są przypisane licencje. Jeśli tak, usuń te licencje i spróbuj ponownie usunąć grupę.

Podobne błędy mogą wystąpić podczas próby usunięcia grupy za pośrednictwem programu PowerShell lub Graph API. Jeśli używasz grupy zsynchronizowanej z lokalnego, usługa Azure AD Connect może również zgłaszać błędy, jeśli nie można usunąć grupy w usłudze Azure AD. We wszystkich takich przypadkach upewnij się, że istnieją jakieś licencje przypisane do grupy i najpierw je usuń.

## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

Jeśli korzystasz z licencjonowania grupowego, warto zapoznać się z poniższą listą ograniczeń i znanych problemów.

- Licencjonowanie na podstawie grupy obecnie nie jest obsługiwane dla grup, które zawierają inne grupy (grupy zagnieżdżone). Jeśli zastosujesz licencję do grupy zagnieżdżonej, zostanie ona przypisana tylko do użytkowników będących bezpośrednimi członkami pierwszego poziomu grupy.

- Tej funkcji można używać tylko z grupami zabezpieczeń i grupami usługi Office 365, które mają securityEnabled=TRUE.

- [Centrum administracyjne usługi Microsoft 365](https://admin.microsoft.com) nie obsługuje obecnie licencjonowania opartego na grupach. Jeśli użytkownik dziedziczy licencję z grupy, ta licencja pojawi się w portalu administracyjnym pakietu Office jako licencja zwykłego użytkownika. Jeśli spróbujesz zmodyfikować tę licencję lub spróbujesz usunąć licencję, portal zwróci komunikat o błędzie. Licencji grupy dziedziczonej nie można modyfikować bezpośrednio na użytkownika.

- Jeśli licencje są przypisywane lub modyfikowane dla dużej grupy (na przykład 100 000 użytkowników), może to wpłynąć na wydajność. W szczególności ilość zmian generowanych przez automatyzację usługi Azure AD może negatywnie wpłynąć na wydajność synchronizacji katalogów między usługą Azure AD i systemami lokalnymi.

- Jeśli używasz grup dynamicznych do zarządzania członkostwem użytkowników, sprawdź, czy dany użytkownik należy do grupy. Jest to wymagane do przypisania licencji. Jeśli tak nie jest, [sprawdź stan przetwarzania reguły członkostwa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) grupy dynamicznej.

- W niektórych sytuacjach wysokiego obciążenia może upłynąć dużo czasu, aby przetworzyć zmiany licencji dla grup lub zmian członkostwa w grupach z istniejącymi licencjami. Jeśli widzisz, że zmiany trwają dłużej niż 24 godziny, aby przetworzyć rozmiar grupy 60K lub mniej, [otwórz bilet pomocy technicznej,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) aby umożliwić nam zbadanie. 

- Automatyzacja zarządzania licencjami nie reaguje automatycznie na wszystkie typy zmian w środowisku. Na przykład może brakować licencji, co powoduje, że niektórzy użytkownicy są w stanie błędu. Aby zwolnić dostępną liczbę miejsc, możesz usunąć niektóre bezpośrednio przypisane licencje od innych użytkowników. Jednak system nie reaguje automatycznie na tę zmianę i naprawić użytkowników w tym stanie błędu.

  Aby obejść te typy ograniczeń, możesz przejść do bloku **grupy** w usłudze Azure AD i kliknąć przycisk **Ponownie przetworzyć**. To polecenie przetwarza wszystkich użytkowników w tej grupie i rozwiązuje stany błędów, jeśli to możliwe.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencjami w ramach programu licencjonowania opartego na grupach, zobacz:

* [Co to jest licencjonowanie oparte na grupach w usłudze Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak migrować użytkowników między licencjami produktów przy użyciu licencjonowania opartego na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Przykłady programu PowerShell dotyczące licencjonowania opartego na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
