---
title: Automatyczne włączanie kopii zapasowych podczas tworzenia maszyny wirtualnej za pomocą usługi Azure Policy
description: Artykuł opisujący sposób używania zasad platformy Azure do automatycznego włączania kopii zapasowej dla wszystkich maszyn wirtualnych utworzonych w danym zakresie
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584272"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatyczne włączanie kopii zapasowych podczas tworzenia maszyny wirtualnej za pomocą usługi Azure Policy

Jednym z kluczowych obowiązków administratora kopii zapasowej lub zgodności w organizacji jest zapewnienie, że wszystkie maszyny o znaczeniu krytycznym dla firmy są archiwizowane przy odpowiednim zachowaniu.

Obecnie usługa Azure Backup udostępnia wbudowane zasady (przy użyciu usługi Azure Policy), które można przypisać do **wszystkich maszyn wirtualnych platformy Azure w określonej lokalizacji w ramach subskrypcji lub grupy zasobów.** Gdy ta zasada jest przypisana do danego zakresu, wszystkie nowe maszyny wirtualne utworzone w tym zakresie są automatycznie konfigurowane do tworzenia kopii zapasowych do **istniejącego magazynu w tej samej lokalizacji i subskrypcji**. Użytkownik może określić przechowalnię i zasady przechowywania, z którymi powinny być skojarzone kopie zapasowe maszyn wirtualnych.

## <a name="supported-scenarios"></a>Scenariusze obsługiwane

* Wbudowane zasady są obecnie obsługiwane tylko dla maszyn wirtualnych platformy Azure. Użytkownicy muszą zadbać o to, aby zasady przechowywania określone podczas przypisywania były zasadami przechowywania maszyn wirtualnych. Zapoznaj się z [tym](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) dokumentem, aby wyświetlić wszystkie jednostki SKU maszyn wirtualnych obsługiwane przez te zasady.

* Zasady można przypisać do jednej lokalizacji i subskrypcji w czasie. Aby włączyć tworzenie kopii zapasowych dla maszyn wirtualnych w różnych lokalizacjach i subskrypcjach, należy utworzyć wiele wystąpień przypisania zasad, po jednym dla każdej kombinacji lokalizacji i subskrypcji.

* Określony magazyn i maszyny wirtualne skonfigurowane do tworzenia kopii zapasowych mogą znajdować się w różnych grupach zasobów.

* Zakres grupy zarządzania jest obecnie nieobsługiwał.

* Wbudowana polityka nie jest obecnie dostępna w chmurach krajowych.

## <a name="using-the-built-in-policy"></a>Korzystanie z wbudowanych zasad

Aby przypisać zasady do wymaganego zakresu, wykonaj poniższe czynności:

1. Zaloguj się do witryny Azure Portal i przejdź do pulpitu nawigacyjnego **zasad.**
2. Wybierz **definicje** w menu po lewej stronie, aby uzyskać listę wszystkich wbudowanych zasad w zasobach platformy Azure.
3. Filtruj listę pod kątem **Kategoria=Kopia zapasowa**. Zostanie wyświetlona lista przefiltrowana do jednej zasady o nazwie "Konfigurowanie kopii zapasowej na maszynach wirtualnych lokalizacji do istniejącej centralnej przechowalni w tej samej lokalizacji".
![Pulpit nawigacyjny zasad](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Kliknij nazwę zasad. Zostaniesz przekierowany do szczegółowej definicji dla tej zasady.
![Ostrze definicji zasad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Kliknij przycisk **Przypisz** u góry ostrza. Spowoduje to przekierowanie do bloku **Przypisywanie zasad.**
6. W obszarze **Podstawy**kliknij trzy kropki obok pola **Zakres.** Spowoduje to otwarcie bloku kontekstu po prawej stronie, w którym można wybrać subskrypcję dla zasad, które mają być stosowane. Można również opcjonalnie wybrać grupę zasobów, tak aby zasady były stosowane tylko dla maszyn wirtualnych w określonej grupie zasobów.
![Podstawowe informacje o przypisywania zasad](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na karcie **Parametry** wybierz lokalizację z listy rozwijanej i wybierz zasady przechowalni i kopii zapasowej, z którymi muszą być skojarzone maszyny wirtualne w zakresie.
![Parametry przypisania zasad](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Upewnij się, że **efekt** jest ustawiony na deployIfNotExists.
9. Przejdź do **pozycji Recenzja i utwórz** i kliknij pozycję **Utwórz**.

> [!NOTE]
>
> Zasady platformy Azure mogą być również używane na istniejących maszynach wirtualnych przy użyciu [korygowania](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Zaleca się, że ta zasada nie jest przypisany do więcej niż 200 maszyn wirtualnych naraz. Jeśli zasada jest przypisana do więcej niż 200 maszyn wirtualnych, może to spowodować wyzwolenie kopii zapasowej kilka godzin później niż określone w harmonogramie.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o zasadach platformy Azure](https://docs.microsoft.com/azure/governance/policy/overview)
