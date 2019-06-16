---
title: Użyj witryny rozwiązań IoT platformy Azure — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia witryny sieci Web AzureIoTSolutions.com wdrożyć Twój akcelerator rozwiązań.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447458"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Użyj witryny azureiotsolutions.com, aby wdrożyć Twój akcelerator rozwiązań

Możesz wdrożyć akceleratorów rozwiązań Azure IoT do Twojej subskrypcji platformy Azure z [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com obsługuje zarówno "open source" firmy Microsoft, jak i akceleratorami rozwiązań partnerskich. Dostosowanie tych akceleratorów rozwiązań [architektura referencyjna IoT platformy](https://aka.ms/iotrefarchitecture). Witryna umożliwia szybkie wdrażanie akceleratora rozwiązań jako środowisko do obsługi demonstracji i produkcyjnych.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Jeśli potrzebujesz większej kontroli nad procesem wdrażania, możesz użyć [interfejsu wiersza polecenia, aby wdrożyć akcelerator rozwiązań](iot-accelerators-remote-monitoring-deploy-cli.md).

Można wdrożyć akceleratorów rozwiązań w następujących konfiguracji:

* **Standardowa**: Wdrożenie infrastruktury rozszerzona na potrzeby tworzenia środowiska produkcyjnego. Mikrousługi są wdrażane na kilku maszynach wirtualnych platformy Azure za pomocą usługi Azure Container Service. Platforma Kubernetes zarządza kontenerami aparatu Docker, w których są hostowane poszczególne mikrousługi.
* **Podstawowe**: Mniejsze koszty w wersji do celów demonstracyjnych i do testowania wdrożenia. Wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej platformy Azure.
* **Lokalne**: Wdrażanie komputera lokalnego na potrzeby projektowania i testowania. To podejście mikrousługi są wdrażane na lokalny kontener platformy Docker i nawiązanie połączenia z Centrum IoT Hub, Azure Cosmos DB i usług Azure storage w chmurze.

Każda z akceleratorami rozwiązań używa kombinacji różnych usług platformy Azure, takich jak usługa IoT Hub, Azure Stream Analytics i Cosmos DB. Aby uzyskać więcej informacji, odwiedź stronę [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) i wybrać akcelerator rozwiązań.

## <a name="sign-in-at-azureiotsolutionscom"></a>Zaloguj się na azureiotsolutions.com

Zanim będzie można wdrożyć akcelerator rozwiązań, musisz się zalogować na AzureIoTSolutions.com przy użyciu poświadczeń skojarzonych z subskrypcją platformy Azure. Jeśli Twoje konto jest skojarzone z więcej niż jednej dzierżawy Microsoft Azure Active Directory (AD), możesz użyć **listy rozwijanej wyboru konta** wybrać katalog używany.

Twoje uprawnienia do wdrażania akceleratorów rozwiązań, zarządzanie użytkownikami i Zarządzaj usługami platformy Azure zależą od Twojej roli w wybranym katalogu. Typowe role usługi Azure AD skojarzonego z akceleratorami rozwiązań obejmują:

* **Administrator globalny**: Może istnieć wiele [Administratorzy globalni](../active-directory/users-groups-roles/directory-assign-admin-roles.md) na dzierżawę usługi Azure AD:

  * Podczas tworzenia dzierżawy usługi Azure AD jest domyślnie administratora globalnego z tej dzierżawy.
  * Administrator globalny, można wdrożyć akceleratorów rozwiązań podstawowa i standardowa.

* **Użytkownik domeny**: Może to być wielu użytkowników domeny dla dzierżawy usługi Azure AD. Użytkownik domeny można wdrożyć akcelerator podstawowego rozwiązania.

* **Użytkownik-Gość**: Może istnieć wiele użytkowników-gości na dzierżawę usługi Azure AD. Użytkownicy-goście nie można wdrożyć akcelerator rozwiązań, w dzierżawie usługi Azure AD.

Aby uzyskać więcej informacji dotyczących użytkowników i ról w usłudze Azure AD zobacz następujące zasoby:

* [Tworzenie użytkowników w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Przypisywanie użytkowników do aplikacji](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Wybierz swoje urządzenie

Łączy lokacji AzureIoTSolutions.com [certyfikatu platformy Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/).

Katalog zawiera setki certyfikowanych urządzeń sprzętowych IoT, którymi można nawiązać Akceleratory rozwiązań, aby rozpocząć tworzenie rozwiązania IoT.

![Katalog urządzeń](media/iot-accelerators-permissions/devicecatalog.png)

Jeśli jesteś producenta sprzętu, kliknij przycisk **Zostań partnerem** Aby dowiedzieć się więcej o partnerstwo z firmą Microsoft w logo Certified for IoT program.

## <a name="next-steps"></a>Kolejne kroki

Aby wypróbować jeden z akceleratorów rozwiązań usługi IoT, zapoznaj się z przewodnikami Szybki start:

* [Testowanie rozwiązania do zdalnego monitorowania](quickstart-remote-monitoring-deploy.md)
* [Testowanie rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Testowanie rozwiązania do konserwacji predykcyjnej](quickstart-predictive-maintenance-deploy.md)
* [Testowanie rozwiązania do symulacji urządzeń](quickstart-device-simulation-deploy.md)
