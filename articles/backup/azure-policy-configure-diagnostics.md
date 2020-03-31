---
title: Konfigurowanie ustawień diagnostyki przechowalni na dużą skalę
description: Konfigurowanie ustawień diagnostyki usługi Log Analytics dla wszystkich magazynów w danym zakresie przy użyciu zasad platformy Azure
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584510"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Konfigurowanie ustawień diagnostyki przechowalni na dużą skalę

Rozwiązanie do raportowania dostarczane przez usługę Azure Backup wykorzystuje usługę Log Analytics (LA). Aby dane danego magazynu były wysyłane do la, należy utworzyć [ustawienie diagnostyki](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) dla tego magazynu.

Często ręczne dodawanie ustawień diagnostycznych na magazyn może być uciążliwym zadaniem. Ponadto każdy nowy utworzony magazyn również musi mieć włączone ustawienia diagnostyki, aby móc wyświetlać raporty dla tego magazynu. 

Aby uprościć tworzenie ustawień diagnostyki na dużą skalę (z LA jako miejscem docelowym), usługa Azure Backup udostępnia wbudowaną [usługę Azure Policy.](https://docs.microsoft.com/azure/governance/policy/) Ta zasada dodaje ustawienie diagnostyki LA do wszystkich magazynów w danej subskrypcji lub grupie zasobów. Poniższe sekcje zawierają instrukcje dotyczące korzystania z tych zasad.

## <a name="supported-scenarios"></a>Scenariusze obsługiwane

* Zasady mogą być stosowane jednocześnie do wszystkich magazynów usług odzyskiwania w określonej subskrypcji (lub do grupy zasobów w ramach subskrypcji). Użytkownik przypisujący zasady musi mieć dostęp "Właściciel" do subskrypcji, do której jest przypisana zasada.

* Obszar roboczy LA określony przez użytkownika (do którego będą wysyłane dane diagnostyczne) może znajdować się w innej subskrypcji niż magazyny, do których przypisano zasady. Użytkownik musi mieć dostęp "Reader", "Contributor" lub "Owner" do subskrypcji, w której istnieje określony obszar roboczy LA.

* Zakres grupy zarządzania jest obecnie nieobsługiwał.

* Wbudowana polityka nie jest obecnie dostępna w chmurach krajowych.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Przypisywanie wbudowanych zasad do zakresu

Aby przypisać zasady dla magazynów w wymaganym zakresie, wykonaj poniższe czynności:

1. Zaloguj się do witryny Azure portal i przejdź do pulpitu nawigacyjnego **zasad.**
2. Wybierz **definicje** w menu po lewej stronie, aby uzyskać listę wszystkich wbudowanych zasad w zasobach platformy Azure.
3. Filtruj listę pod kątem **Kategoria=Monitorowanie**. Znajdź obszar **roboczy usługi [Podgląd]: Wdrażanie ustawień diagnostycznych dla magazynu usług odzyskiwania w obszarze roboczym usługi Log Analytics dla kategorii specyficznych dla zasobów**.

![Ostrze definicji zasad](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Kliknij nazwę zasad. Zostaniesz przekierowany do szczegółowej definicji dla tej zasady.

![Szczegółowa definicja zasad](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Kliknij przycisk **Przypisz** u góry ostrza. Spowoduje to przekierowanie do bloku **Przypisywanie zasad.**

6. W obszarze **Podstawy**kliknij trzy kropki obok pola **Zakres.** Spowoduje to otwarcie bloku kontekstu po prawej stronie, w którym można wybrać subskrypcję dla zasad, które mają być stosowane. Można również opcjonalnie wybrać grupę zasobów, tak aby zasada była stosowana tylko dla przechowalni w określonej grupie zasobów.

![Podstawowe informacje o przypisywania zasad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. W obszarze **Parametry**wprowadź następujące informacje:

* **Nazwa profilu** — nazwa, która zostanie przypisana do ustawień diagnostyki utworzonych przez zasady.
* **Obszar roboczy analizy dzienników** — obszar roboczy usługi Log Analytics, z którym powinno być skojarzone ustawienie diagnostyki. Dane diagnostyczne wszystkich magazynów w zakresie przypisania zasad zostaną wypchnięte do określonego obszaru roboczego LA Workspace.

* **Nazwa znacznika wykluczeń (opcjonalnie) i wartość znacznika wykluczenia (opcjonalnie)** — można wykluczyć z przypisania zasad przechowalnie zawierające określoną nazwę i wartość znacznika. Na przykład, jeśli **nie** chcesz, aby ustawienie diagnostyki zostało dodane do tych magazynów, w których znacznik "isTest" jest ustawiony na wartość "tak", należy wprowadzić "isTest" w polu **Nazwa tagu wykluczania** i "tak" w polu **Wartość znacznika wykluczenia.** Jeśli którekolwiek (lub oba) z tych dwóch pól pozostaną puste, zasady zostaną zastosowane do wszystkich odpowiednich magazynów, niezależnie od tagów, które zawierają.

![Parametry przypisania zasad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Utwórz zadanie korygowania** — po przypisaniu zasad do zakresu wszystkie nowe magazyny utworzone w tym zakresie automatycznie otrzymują skonfigurowane ustawienia diagnostyki LA (w ciągu 30 minut od momentu utworzenia magazynu). Aby dodać ustawienie diagnostyki do istniejących magazynów w zakresie, można wyzwolić zadanie korygowania w czasie przypisywania zasad. Aby wyzwolić zadanie korygowania, zaznacz pole wyboru **Utwórz zadanie korygowania**. 

![Korygowanie przypisania zasad](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Przejdź do karty **Recenzja+Utwórz** i kliknij pozycję **Utwórz**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Na jakich warunkach zadanie korygowania będzie miało zastosowanie do przechowalni?

Zadanie korygowania jest stosowane do magazynów, które są niezgodne zgodnie z definicją zasad. Przechowalnia jest niezgodna, jeśli spełnia jeden z następujących warunków:

* Nie ma ustawienia diagnostyki dla przechowalni.
* Ustawienia diagnostyczne są obecne dla magazynu, ale żadne z ustawień nie ma wszystkich zdarzeń specyficznych dla **zasobu włączonych** z LA jako miejsce docelowe i **Specyficzne dla zasobu** wybrane w przełączniku. 

Tak więc, nawet jeśli użytkownik ma magazyn z włączoną usługą AzureBackupReport w trybie AzureDiagnostics (który jest obsługiwany przez raporty kopii zapasowej), zadanie korygowania będzie nadal stosowane do tego magazynu, ponieważ tryb specyficzny dla zasobu jest zalecanym sposobem tworzenia ustawień diagnostycznych, [w przyszłości.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)

Ponadto jeśli użytkownik ma magazyn z włączoną tylko podzbiórem sześciu zdarzeń specyficznych dla zasobu, zadanie korygowania będzie stosowane do tego magazynu, ponieważ raporty kopii zapasowej będą działać zgodnie z oczekiwaniami tylko wtedy, gdy wszystkie z sześciu zdarzeń specyficznych dla zasobu są włączone.

> [!NOTE]
>
> Jeśli magazyn ma istniejące ustawienie diagnostyczne z **włączonym podzbiorem kategorii specyficznych dla zasobów,** skonfigurowanym do wysyłania danych do określonego obszaru roboczego LA, powiedz "Obszar roboczy X", zadanie korygowania zakończy się niepowodzeniem (tylko dla tego magazynu), jeśli docelowy obszar roboczy LA workspace podany w przypisaniu zasad jest **taki sam** "Obszar roboczy X". 
>
>Dzieje się tak, ponieważ jeśli zdarzenia włączone przez dwa różne ustawienia diagnostyki na tym samym zasobie **nakładają się** w jakiejś formie, ustawienia nie mogą mieć tego samego obszaru roboczego LA co miejsce docelowe. Należy ręcznie rozwiązać ten błąd, przechodząc do odpowiedniego magazynu i konfigurując ustawienie diagnostyki z innym obszarem roboczym LA jako miejscem docelowym.
>
> Należy zauważyć, że zadanie korygowania **nie** zakończy się niepowodzeniem, jeśli istniejące ustawienie diagnostyki jako tylko AzureBackupReport włączone z workspace X jako miejsce docelowe, ponieważ w tym przypadku nie będzie nakładać się między zdarzeniami włączonymi przez istniejące ustawienie i zdarzenia włączone przez ustawienie utworzone przez zadanie korygowania.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak korzystać z raportów kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports)
* [Dowiedz się więcej o zasadach platformy Azure](https://docs.microsoft.com/azure/governance/policy/)
* [Użyj zasady platformy Azure, aby automatycznie włączyć tworzenie kopii zapasowych dla wszystkich maszyn wirtualnych w określonym zakresie](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
