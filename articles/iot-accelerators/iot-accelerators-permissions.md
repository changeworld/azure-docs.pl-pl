---
title: Korzystanie z witryny rozwiązań Azure IoT — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak za pomocą AzureIoTSolutions.com witryny sieci Web, aby wdrożyć akcelerator rozwiązań.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447458"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Wdrażanie akceleratora rozwiązań za pomocą lokacji azureiotsolutions.com

Akceleratory rozwiązań Azure IoT można wdrożyć w ramach subskrypcji platformy Azure z [AzureIoTSolutions.com.](https://www.azureiotsolutions.com/Accelerators) AzureIoTSolutions.com obsługuje zarówno akceleratory rozwiązań open source, jak i partnerskie firmy Microsoft. Te akceleratory rozwiązań są zgodne z [architekturą referencyjną Usługi Azure IoT.](https://aka.ms/iotrefarchitecture) Lokacji można użyć do szybkiego wdrożenia akceleratora rozwiązań jako środowiska demonstracyjnego lub produkcyjnego.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Jeśli potrzebujesz większej kontroli nad procesem wdrażania, możesz użyć [interfejsu wiersza polecenia do wdrożenia akceleratora rozwiązań](iot-accelerators-remote-monitoring-deploy-cli.md).

Akceleratory rozwiązań można wdrożyć w następujących konfiguracjach:

* **Standard**: Rozszerzone wdrożenie infrastruktury do tworzenia środowiska produkcyjnego. Mikrousługi są wdrażane na kilku maszynach wirtualnych platformy Azure za pomocą usługi Azure Container Service. Platforma Kubernetes zarządza kontenerami aparatu Docker, w których są hostowane poszczególne mikrousługi.
* **Podstawowe:** Wersja o obniżonym koszcie dla demonstracji lub do testowania wdrożenia. Wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej platformy Azure.
* **Lokalny:** Wdrożenie komputera lokalnego do testowania i tworzenia. Takie podejście wdraża mikrousługi w lokalnym kontenerze platformy Docker i łączy się z usługami Usługi IoT Hub, usługi Azure Cosmos DB i usługi magazynu platformy Azure w chmurze.

Każdy z akceleratorów rozwiązań używa innej kombinacji usług platformy Azure, takich jak Usługa IoT Hub, usługa Azure Stream Analytics i usługa Cosmos DB. Aby uzyskać więcej informacji, odwiedź [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) i wybierz akcelerator rozwiązań.

## <a name="sign-in-at-azureiotsolutionscom"></a>Zaloguj się na azureiotsolutions.com

Aby można było wdrożyć akcelerator rozwiązań, należy zalogować się w AzureIoTSolutions.com przy użyciu poświadczeń skojarzonych z subskrypcją platformy Azure. Jeśli Twoje konto jest skojarzone z więcej niż jedną dzierżawą usługi Microsoft Azure Active Directory (AD), możesz użyć **listy rozwijanej Wybór konta,** aby wybrać katalog do użycia.

Twoje uprawnienia do wdrażania akceleratorów rozwiązań, zarządzania użytkownikami i zarządzania usługami platformy Azure zależą od twojej roli w wybranym katalogu. Typowe role usługi Azure AD skojarzone z akceleratorami rozwiązań obejmują:

* **Administrator globalny:** Może istnieć wielu [administratorów globalnych](../active-directory/users-groups-roles/directory-assign-admin-roles.md) na dzierżawę usługi Azure AD:

  * Podczas tworzenia dzierżawy usługi Azure AD, jesteś domyślnie administratorem globalnym tej dzierżawy.
  * Administrator globalny może wdrażać podstawowe i standardowe akceleratory rozwiązań.

* **Użytkownik domeny:** Może istnieć wielu użytkowników domeny na dzierżawę usługi Azure AD. Użytkownik domeny może wdrożyć podstawowy akcelerator rozwiązań.

* **Użytkownik-gość:** Może istnieć wielu użytkowników-gości na dzierżawę usługi Azure AD. Użytkownicy-goście nie mogą wdrożyć akceleratora rozwiązań w dzierżawie usługi Azure AD.

Aby uzyskać więcej informacji na temat użytkowników i ról w usłudze Azure AD, zobacz następujące zasoby:

* [Tworzenie użytkowników w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Przypisywanie użytkowników do aplikacji](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Wybierz urządzenie

Witryna AzureIoTSolutions.com łączy się z [katalogiem urządzeń certyfikatu platformy Azure dla IoT](https://catalog.azureiotsolutions.com/).

Katalog zawiera listę setek certyfikowanych urządzeń sprzętowych IoT, które można połączyć z akceleratorami rozwiązań, aby rozpocząć tworzenie rozwiązania IoT.

![Katalog urządzeń](media/iot-accelerators-permissions/devicecatalog.png)

Jeśli jesteś producentem sprzętu, kliknij pozycję **Zostań partnerem,** aby dowiedzieć się więcej o współpracy z firmą Microsoft w programie Certyfikowany dla IoT.

## <a name="next-steps"></a>Następne kroki

Aby wypróbować jeden z akceleratorów rozwiązań usługi IoT, zapoznaj się z przewodnikami Szybki start:

* [Testowanie rozwiązania do monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)
* [Testowanie rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Testowanie rozwiązania do konserwacji predykcyjnej](quickstart-predictive-maintenance-deploy.md)
* [Testowanie rozwiązania do symulacji urządzeń](quickstart-device-simulation-deploy.md)
