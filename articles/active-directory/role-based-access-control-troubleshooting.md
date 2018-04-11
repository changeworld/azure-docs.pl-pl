---
title: Rozwiązywanie problemów z kontroli dostępu opartej na rolach Azure RBAC | Dokumentacja firmy Microsoft
description: Uzyskaj pomoc dotyczącą problemy lub pytania dotyczące zasobów kontroli dostępu opartej na rolach.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: seohack1
ms.openlocfilehash: 766ff118638538520c8f17694b32f35dbe6d1025
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-azure-role-based-access-control"></a>Rozwiązywanie problemów z kontroli dostępu opartej na rolach na platformie Azure 

Ten artykuł zawiera odpowiedzi często zadawane pytania dotyczące określone prawa dostępu przyznane z rolami, tak aby wiedzieli, czego można oczekiwać, korzystając z ról w portalu Azure i może rozwiązać problemy z dostępem do. Te trzy role opisano wszystkie typy zasobów:

* Właściciel  
* Współautor  
* Czytelnik  

Właściciele i współautorzy mają pełny dostęp do możliwości zarządzania, ale współautora nie może udzielić dostępu do innych użytkowników lub grup. Elementy poznasz nieco bardziej interesującego z rolą czytnika, dzięki czemu to, gdzie będzie poświęcić trochę czasu. Zobacz [opartej na rolach kontrola dostępu get-started artykułu](role-based-access-control-configure.md) szczegółowe informacje na temat sposobu udzielić dostępu.

## <a name="app-service"></a>App Service
### <a name="write-access-capabilities"></a>Możliwości zapisu
Przyznanie dostępu użytkowników tylko do odczytu w aplikacji sieci web jednej, niektóre funkcje zostały wyłączone, że nie może spodziewać się. Następujące funkcje zarządzania wymagają **zapisu** dostęp do aplikacji sieci web (współautora lub właściciela), a nie są dostępne w jakimkolwiek scenariuszu tylko do odczytu.

* Polecenia (na przykład Uruchom, Zatrzymaj, itp.)
* Zmiana ustawień, takich jak konfiguracja ogólna, ustawienia skalowania ustawienia kopii zapasowej i ustawienia monitorowania
* Uzyskiwanie dostępu do poświadczeń publikowania i innych informacji poufnych, takich jak ustawienia aplikacji i parametry połączenia
* Przesyłanie strumieniowe dzienników
* Dzienniki diagnostyczne konfiguracji
* W konsoli (wiersza polecenia)
* Ostatnie i Active wdrożenia (ciągłe wdrażanie lokalnej git)
* Szacowane wydatki
* Testy sieci Web
* Sieć wirtualna (widoczne tylko dla czytnika, jeśli wcześniej skonfigurowano sieci wirtualnej przez użytkownika z uprawnieniami do zapisu).

Jeśli nie masz dostępu do żadnego z tych kafelków, należy skontaktować się z administratorem współautora dostępu do aplikacji sieci web.

### <a name="dealing-with-related-resources"></a>Zajmujących się zasoby pokrewne
Aplikacje sieci Web są skomplikowane obecności kilka różnych zasobów, które wzajemne oddziaływanie. W tym miejscu jest grupą typowych zasobów z kilku witryn sieci Web:

![Grupa zasobów aplikacji sieci Web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Dzięki temu, jeśli ktoś dostępu tylko w aplikacji sieci web, wiele funkcji w bloku witryny sieci Web w portalu Azure jest wyłączona.

Te elementy wymagają **zapisu** dostęp do **planu usługi aplikacji** odnosi się do witryny sieci Web:  

* Wyświetlanie aplikacji sieci web firmy cenowym (wolne lub standardowy)  
* Skala konfiguracji (liczba wystąpień, rozmiar maszyny wirtualnej, ustawienia skalowania automatycznego)  
* Przydziały (magazynu, przepustowości, procesor CPU)  

Te elementy wymagają **zapisu** dostęp do całego **grupy zasobów** zawierający witryny sieci Web:  

* Certyfikaty SSL i powiązań (certyfikaty SSL można udostępniać między lokacjami w tej samej grupie zasobów i lokalizacja geograficzna)  
* Reguły alertów  
* Ustawienia skalowania automatycznego  
* Składniki usługi Application Insights  
* Testy sieci Web  

## <a name="azure-functions"></a>Azure Functions
Niektóre funkcje [usługi Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Na przykład jeśli użytkownik jest przypisać rolę czytelnika, nie będą mogli wyświetlać funkcje w aplikacji funkcji. Zostaną wyświetlone w portalu **(bez dostępu)**.

![Funkcja Brak dostępu do aplikacji](./media/role-based-access-control-troubleshooting/functionapps-noaccess.png)

Można kliknąć czytnik **funkcji platformy** a następnie kliknij pozycję **wszystkie ustawienia** Aby wyświetlić niektóre ustawienia związane z aplikacji funkcji (podobnie do aplikacji sieci web), ale nie mogą modyfikować tych ustawień.

## <a name="virtual-machine"></a>Maszyna wirtualna
Wiele takich jak z aplikacjami sieci web, niektóre funkcje w bloku maszyny wirtualnej wymagają dostęp do zapisu do maszyny wirtualnej lub do innych zasobów w grupie zasobów.

Maszyny wirtualne są powiązane z nazwy domeny, sieci wirtualne, konta magazynu i reguł alertów.

Te elementy wymagają **zapisu** dostęp do **maszyny wirtualnej**:

* Punkty końcowe  
* Adresy IP  
* Dyski  
* Rozszerzenia  

Wymagają one wykonywania **zapisu** dostęp do **maszyny wirtualnej**i **grupy zasobów** (wraz z nazwą domeny) jest it:  

* Zestaw dostępności  
* Zestawu o zrównoważonym obciążeniu  
* Reguły alertów  

Jeśli nie masz dostępu do żadnego z tych kafelków, skontaktuj się z administratorem współautora dostępu do grupy zasobów.

## <a name="see-more"></a>Zobacz więcej
* [Kontroli dostępu opartej na rolach](role-based-access-control-configure.md): rozpoczynanie pracy z RBAC w portalu Azure.
* [Wbudowane role](role-based-access-built-in-roles.md): uzyskiwanie szczegółowych informacji dotyczących ról, które standardowo w RBAC.
* [Role niestandardowe w Azure RBAC](role-based-access-control-custom-roles.md): Dowiedz się, jak tworzyć role niestandardowe, aby spełniały Twoje potrzeby dostępu.
* [Tworzenie raportu historii zmian dostępu](role-based-access-control-access-change-history-report.md): informacje o zmieniania przypisań ról w RBAC.

