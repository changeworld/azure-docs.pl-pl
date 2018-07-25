---
title: Rozwiązywanie problemów z RBAC na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów przy użyciu kontroli dostępu opartej na rolach na platformie Azure (RBAC).
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
ms.date: 07/23/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d1a0e46fe348bbc60a4d02a4727a9bb27cb26742
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223300"
---
# <a name="troubleshoot-rbac-in-azure"></a>Rozwiązywanie problemów z RBAC na platformie Azure

W tym artykule odpowiedzi na często zadawane pytania dotyczące kontroli dostępu opartej na rolach (RBAC), dzięki czemu będzie wiadomo, czego można oczekiwać, gdy przy użyciu ról w witrynie Azure portal i możesz rozwiązać problemy z dostępem do.

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
* Zestawu o zrównoważonym obciążeniu  
* Reguły alertów  

Jeśli nie masz dostępu do żadnego z tych kafelków, poproś administratora dostępu współautora do grupy zasobów.

## <a name="azure-functions-and-write-access"></a>Usługa Azure Functions i dostęp do zapisu

Niektóre funkcje [usługi Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Na przykład jeśli użytkownik jest przypisany roli Czytelnik, nie będą mogli wyświetlać funkcje w ramach aplikacji funkcji. Wyświetli portalu **(Brak dostępu)**.

![Brak dostępu aplikacje funkcji](./media/troubleshooting/functionapps-noaccess.png)

Czytelnik mogą kliknąć **funkcje platformy** kartę, a następnie kliknij przycisk **wszystkie ustawienia** Aby wyświetlić niektóre ustawienia związane z aplikacją funkcji (podobne do aplikacji sieci web), ale nie mogą modyfikować dowolne z tych ustawień.

## <a name="rbac-changes-are-not-being-detected"></a>RBAC zmiany nie są wykrywane

Usługa Azure Resource Manager czasami przechowuje w pamięci podręcznej konfiguracji i danych w celu zwiększenia wydajności. Podczas tworzenia lub usuwania przypisania roli, może potrwać do 30 minut, aby zmiany zaczęły obowiązywać. Jeśli używasz witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure, możesz wymusić odświeżenie zmiany przypisania roli, wylogowywania i logowania. Jeśli w przypadku wprowadzania zmian przypisania roli przy użyciu wywołań interfejsu API REST, możesz wymusić odświeżenie, odświeżając tokenu dostępu.

## <a name="next-steps"></a>Kolejne kroki
* [Manage access using RBAC and the Azure portal (Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal)](role-assignments-portal.md)
* [Wyświetlanie dzienników aktywności dla zmian RBAC](change-history-report.md)

