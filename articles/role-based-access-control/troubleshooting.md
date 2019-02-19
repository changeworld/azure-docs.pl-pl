---
title: Rozwiązywanie problemów z funkcji RBAC dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów przy użyciu kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 7b27c811214def7f5646f886b955d035a50c0725
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342477"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Rozwiązywanie problemów z funkcji RBAC dla zasobów platformy Azure

W tym artykule odpowiedzi na często zadawane pytania dotyczące kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure, dzięki czemu będzie wiadomo, czego można oczekiwać, gdy przy użyciu ról w witrynie Azure portal i możesz rozwiązać problemy z dostępem do.

## <a name="problems-with-rbac-role-assignments"></a>Problemy z przypisaniami ról kontroli dostępu opartej na rolach

- Jeśli nie można dodać przypisania roli, ponieważ jesteś **Dodaj przypisanie roli** opcja jest wyłączona, lub ponieważ otrzymasz błąd uprawnień, sprawdź, czy używasz roli, która ma `Microsoft.Authorization/roleAssignments/*` uprawnień w zakresie, który próbujesz Przypisz rolę. Jeśli nie masz tego uprawnienia, skontaktuj się z administratorem subskrypcji.
- Jeśli wystąpi błąd uprawnień podczas próby utworzenia zasobu, sprawdź, czy używasz roli, który ma uprawnienia do tworzenia zasobów w wybranym zakresie. Na przykład konieczne może być współautorem. Jeśli nie masz uprawnień, skontaktuj się z administratorem subskrypcji.
- Jeśli wystąpi błąd uprawnień podczas próby utworzenia lub zaktualizowania bilet pomocy technicznej, sprawdź, czy używasz roli, która ma `Microsoft.Support/*` uprawnienia, takie jak [Współautor żądania obsługi](built-in-roles.md#support-request-contributor).
- Jeśli podczas próby przypisania roli jest zwracany błąd informujący, że przekroczono liczbę przypisań ról, spróbuj zmniejszyć liczbę przypisań ról, przypisując zamiast tego role do grup. Platforma Azure obsługuje maksymalnie **2000** przypisania roli na subskrypcję.

## <a name="problems-with-custom-roles"></a>Problemy z rolami niestandardowymi

- Jeśli nie można zaktualizować istniejącej roli niestandardowej Sprawdź, czy masz `Microsoft.Authorization/roleDefinition/write` uprawnień.
- Jeśli nie można zaktualizować istniejącej roli niestandardowej Sprawdź, czy co najmniej jeden zakres możliwy do przypisania zostały usunięte w dzierżawie. `AssignableScopes` Właściwość dla formantów roli niestandardowej [kto może tworzyć, usunąć, zaktualizować lub wyświetlić rolę niestandardową](custom-roles.md#who-can-create-delete-update-or-view-a-custom-role).
- Jeśli wystąpi błąd, który przekroczył limit definicji ról, podczas próby utworzenia nowej roli, Usuń role niestandardowe, które nie są używane. Możesz również spróbować skonsolidować ani nie używaj ponownie wszystkie istniejące role niestandardowe. Platforma Azure obsługuje maksymalnie **2000** ról niestandardowych w dzierżawie.
- Jeśli nie można usunąć rolę niestandardową, sprawdź, czy co najmniej jedno przypisanie roli nadal używają niestandardowej roli.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Odzyskiwanie kontroli dostępu opartej na rolach po przeniesieniu subskrypcji między dzierżawami

- Jeśli potrzebujesz kroków dotyczących przenoszenia subskrypcji do innej dzierżawy, zobacz [Przenoszenie własności subskrypcji platformy Azure na inne konto](../billing/billing-subscription-transfer.md).
- W przypadku przeniesienia subskrypcji do innej dzierżawy, wszystkie przypisania roli zostaną trwale usunięte z dzierżawy źródła i nie są migrowane do dzierżawy docelowej. Należy ponownie utworzyć przypisania roli w dzierżawie docelowej.
- Jeśli jesteś administratora globalnego i utracić dostęp do subskrypcji, użyj **Access management dla zasobów platformy Azure** przełącznik, aby tymczasowo [podniesienie poziomu dostępu](elevate-access-global-admin.md) Aby odzyskać dostęp do Subskrypcja.

## <a name="rbac-changes-are-not-being-detected"></a>RBAC zmiany nie są wykrywane

Usługa Azure Resource Manager czasami przechowuje w pamięci podręcznej konfiguracji i danych w celu zwiększenia wydajności. Podczas tworzenia lub usuwania przypisania roli, może potrwać do 30 minut, aby zmiany zaczęły obowiązywać. Jeśli używasz witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure, możesz wymusić odświeżenie zmiany przypisania roli, wylogowywania i logowania. Jeśli w przypadku wprowadzania zmian przypisania roli przy użyciu wywołań interfejsu API REST, możesz wymusić odświeżenie, odświeżając tokenu dostępu.

## <a name="web-app-features-that-require-write-access"></a>Funkcje aplikacji sieci Web, które wymagają dostępu do zapisu

Jeśli możesz udzielić użytkownikowi dostępu tylko do odczytu do pojedynczej aplikacji sieci web, niektóre funkcje są wyłączone, że może nie oczekujesz. Następujące funkcje zarządzania wymagają **zapisu** dostęp do aplikacji sieci web (współautora lub właściciela), a nie są dostępne w każdym scenariuszu tylko do odczytu.

* Poleceń (takich jak uruchamianie, zatrzymywanie i itp.)
* Zmiany ustawień, takich jak konfiguracja ogólna, ustawienia skalowania, ustawienia kopii zapasowej i ustawienia monitorowania
* Uzyskiwanie dostępu do poświadczeń publikowania i inne wpisy tajne, takie jak ustawienia aplikacji i parametry połączenia
* Przesyłanie strumieniowe dzienników
* Dzienniki diagnostyczne konfiguracji
* W konsoli (wiersza polecenia)
* Aktywne i ostatnie wdrożenia (ciągłe wdrażanie lokalnej usługi git)
* Szacowane wydatki
* Testy sieci Web
* Sieć wirtualna (widoczne tylko dla czytnika, jeśli wcześniej skonfigurowano sieci wirtualnej przez użytkownika z uprawnieniami do zapisu).

Jeśli nie masz dostępu do żadnego z tych kafelków, należy poprosić administratora o prawa dostępu współautora do aplikacji sieci web.

## <a name="web-app-resources-that-require-write-access"></a>Zasoby aplikacji sieci Web, które wymagają dostępu do zapisu

Aplikacje sieci Web są skomplikowane przez obecność kilku różnych zasobów, które wzajemne oddziaływania wykresów. Poniżej przedstawiono grupy typowych zasobów przy użyciu kilku witryn sieci Web:

![Grupy zasobów aplikacji sieci Web](./media/troubleshooting/website-resource-model.png)

W rezultacie, gdy przyznasz ktoś dostęp tylko w aplikacji sieci web, wiele funkcji w bloku witryny sieci Web w witrynie Azure portal jest wyłączony.

Te elementy wymagają **zapisu** dostęp do **planu usługi App Service** odnosi się do witryny sieci Web:  

* Wyświetlanie w aplikacji sieci web cenowych warstwy (bezpłatna lub standardowa)  
* Konfiguracja skalowania (liczba wystąpień, rozmiar maszyny wirtualnej, ustawienia automatycznego skalowania)  
* Przydziały (magazynu, przepustowości, procesor CPU)  

Te elementy wymagają **zapisu** dostęp do całego **grupy zasobów** zawierający witryny sieci Web:  

* Certyfikaty SSL i powiązań (certyfikaty SSL mogą być współużytkowane między lokacjami w tej samej grupie zasobów i lokalizacja geograficzna)  
* Reguły alertów  
* Ustawienia automatycznego skalowania  
* Składniki usługi Application Insights  
* Testy sieci Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkcje maszyny wirtualnej, które wymagają dostępu do zapisu

Podobnie jak w aplikacji sieci web, niektóre funkcje, w bloku maszyny wirtualnej wymagają dostępu do zapisu do maszyny wirtualnej lub do innych zasobów w grupie zasobów.

Maszyny wirtualne są związane z nazwy domeny, sieci wirtualne, konta magazynu i reguł alertów.

Te elementy wymagają **zapisu** dostęp do **maszyny wirtualnej**:

* Punkty końcowe  
* Adresy IP  
* Dyski  
* Rozszerzenia  

Wymagają one **zapisu** dostęp do obu **maszyny wirtualnej**i **grupy zasobów** (wraz z nazwą domeny) aby znajdował się w:  

* Zestaw dostępności  
* Zestaw o zrównoważonym obciążeniu  
* Reguły alertów  

Jeśli nie masz dostępu do żadnego z tych kafelków, poproś administratora dostępu współautora do grupy zasobów.

## <a name="azure-functions-and-write-access"></a>Usługa Azure Functions i dostęp do zapisu

Niektóre funkcje [usługi Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Na przykład jeśli użytkownik jest przypisany roli Czytelnik, nie będą mogli wyświetlać funkcje w ramach aplikacji funkcji. Wyświetli portalu **(Brak dostępu)**.

![Brak dostępu aplikacje funkcji](./media/troubleshooting/functionapps-noaccess.png)

Czytelnik mogą kliknąć **funkcje platformy** kartę, a następnie kliknij przycisk **wszystkie ustawienia** Aby wyświetlić niektóre ustawienia związane z aplikacją funkcji (podobne do aplikacji sieci web), ale nie mogą modyfikować dowolne z tych ustawień.

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i witryny Azure portal](role-assignments-portal.md)
* [Wyświetlanie dzienników aktywności dla zmian RBAC do zasobów platformy Azure](change-history-report.md)

