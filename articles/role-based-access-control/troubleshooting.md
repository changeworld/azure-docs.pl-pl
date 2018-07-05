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
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 186bcf26639f5cff2dcbf1e805913ac7edab7df4
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437370"
---
# <a name="troubleshooting-rbac-in-azure"></a>Rozwiązywanie problemów z RBAC na platformie Azure

W tym artykule odpowiedzi na często zadawane pytania dotyczące kontroli dostępu opartej na rolach (RBAC), dzięki czemu będzie wiadomo, czego można oczekiwać, gdy przy użyciu ról w witrynie Azure portal i możesz rozwiązać problemy z dostępem do. Te trzy role obejmują wszystkie typy zasobów:

* Właściciel  
* Współautor  
* Czytelnik  

Właściciele i współautorzy mają pełny dostęp do środowiska zarządzania, ale Współautor nie może przyznawać dostępu innym użytkownikom lub grupom. Elementy Pobierz nieco bardziej interesująca z rolą Czytelnik tak to, gdzie będzie poświęcić trochę czasu. Aby uzyskać informacje o tym, jak udzielić dostępu, seee [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](role-assignments-portal.md).

## <a name="app-service"></a>App Service
### <a name="write-access-capabilities"></a>Możliwości dostępu do zapisu
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

### <a name="dealing-with-related-resources"></a>Obsługa wraz z powiązanymi zasobami
Aplikacje sieci Web są skomplikowane przez obecność kilku różnych zasobów, które wzajemne oddziaływania wykresów. Oto grupę zasobów typowe za kilka witryn sieci Web:

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

## <a name="azure-functions"></a>Azure Functions
Niektóre funkcje [usługi Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Na przykład jeśli użytkownik jest przypisany roli Czytelnik, nie będą mogli wyświetlać funkcje w ramach aplikacji funkcji. Wyświetli portalu **(Brak dostępu)**.

![Brak dostępu aplikacje funkcji](./media/troubleshooting/functionapps-noaccess.png)

Czytelnik mogą kliknąć **funkcje platformy** kartę, a następnie kliknij przycisk **wszystkie ustawienia** Aby wyświetlić niektóre ustawienia związane z aplikacją funkcji (podobne do aplikacji sieci web), ale nie mogą modyfikować dowolne z tych ustawień.

## <a name="virtual-machine"></a>Maszyna wirtualna
Znacznie takich jak w aplikacji sieci web, niektóre funkcje, w bloku maszyny wirtualnej jest wymagany dostęp do zapisu do maszyny wirtualnej lub do innych zasobów w grupie zasobów.

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

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](role-assignments-portal.md)
* [Wyświetlanie dzienników aktywności dla zmian RBAC](change-history-report.md)

