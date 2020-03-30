---
title: Zarządzanie modelami IoT Plug and Play Preview w repozytorium| Dokumenty firmy Microsoft
description: Jak zarządzać modelami możliwości urządzenia w repozytorium przy użyciu portalu Certyfikat azure dla IoT, interfejsu wiersza polecenia platformy Azure i kodu programu Visual Studio.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159153"
---
# <a name="manage-models-in-the-repository"></a>Zarządzanie modelami w repozytorium

Repozytorium modelu IoT Plug and Play Preview przechowuje modele i interfejsy funkcji urządzenia. Repozytorium sprawia, że modele i interfejsy wykrywalne i materiały eksploatacyjne przez deweloperów rozwiązań.

Istnieją trzy narzędzia, za pomocą których można zarządzać repozytorium:

- Portal z certyfikatem platformy Azure dla IoT
- Interfejs wiersza polecenia platformy Azure
- Visual Studio Code

## <a name="model-repositories"></a>Repozytoria modeli

Istnieją dwa typy repozytorium modeli do przechowywania modeli możliwości urządzenia i interfejsów:

- Istnieje jedno _publiczne repozytorium,_ które przechowuje modele możliwości urządzenia i interfejsy dla urządzeń w [katalogu urządzeń certyfikatu platformy Azure dla IoT.](https://aka.ms/iotdevcat) To repozytorium [przechowuje](./concepts-common-interfaces.md) również wspólne interfejsy i [dcm i interfejsy opublikowane przez microsoft partners](./howto-onboard-portal.md). Aby dowiedzieć się, jak certyfikować urządzenie i dodać jego model możliwości urządzenia do publicznego repozytorium, zobacz samouczek [Certyfikuj urządzenie IoT Plug and Play](./tutorial-certification-test.md).
- Istnieje wiele _repozytoriów firmy._ Repozytorium firmy jest tworzone automatycznie dla twojej organizacji, gdy [korzystasz z portalu Certyfikat platformy Azure dla IoT.](./howto-onboard-portal.md) Za pomocą repozytorium firmowego można przechowywać modele i interfejsy funkcji urządzenia podczas tworzenia i testowania.

## <a name="azure-certified-for-iot-portal"></a>Portal z certyfikatem platformy Azure dla IoT

W [portalu Certyfikat platformy Azure dla IoT](https://preview.catalog.azureiotsolutions.com)można wykonać następujące zadania:

- [Zakończ proces certyfikacji urządzenia IoT.](./tutorial-certification-test.md)
- Znajdź modele urządzeń Typu Plug and Play IoT. Za pomocą tych modeli można szybko tworzyć urządzenia gotowe do [IoT i integrować je z rozwiązaniami.](./quickstart-connect-pnp-device-solution-node.md)

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure udostępnia polecenia do zarządzania modelami i interfejsami możliwości urządzeń w repozytoriach publicznych i firmowych i repozytoriach modeli IoT Plug and Play. Aby uzyskać więcej informacji, zobacz [instalowanie i używanie rozszerzenia Azure IoT dla narzędzia Azure CLI—](./howto-install-pnp-cli.md) przewodnik po instrukcjach.

## <a name="visual-studio-code"></a>Visual Studio Code

Aby otworzyć widok **repozytorium modelu** w programie Visual Studio Code.

1. Otwórz program Visual Studio Code, użyj **klawiszy Ctrl+Shift+P**, wpisz i wybierz opcję **IoT Plug and Play: Otwórz repozytorium modelu**.

1. Można wybrać **opcję Otwórz repozytorium modeli publicznych** lub **Otwórz repozytorium modeli organizacyjnych**. W przypadku repozytorium modelu firmy należy wprowadzić parametry połączenia repozytorium modelu. Ten ciąg połączenia można znaleźć w [portalu Certyfikat platformy Azure dla IoT](https://preview.catalog.azureiotsolutions.com) na karcie Parametry **połączenia** dla **repozytorium firmy.**

1. Nowa karta otwiera widok **Repozytorium modelu.**

    Ten widok służy do dodawania, pobierania i usuwania modeli i interfejsów z możliwościami urządzeń. Można użyć filtru, aby znaleźć określone elementy na liście.

1. Aby przełączać się między repozytorium modelu firmowego a publicznym repozytorium modelu, użyj **klawiszy Ctrl+Shift+P**, wpisz i wybierz **opcję IoT Plug and Play: Wyloguj repozytorium modeli**. Następnie ponownie użyj polecenia **IoT Plug and Play: Otwórz repozytorium modelu.**

> [!NOTE]
> W programie VS Code publiczne repozytorium modelu jest tylko do odczytu. Partnerzy firmy Microsoft mogą aktualizować publiczne repozytorium w [portalu Certyfikat platformy Azure dla IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Następne kroki

Sugerowanym następnym krokiem jest nauczenie się [przesyłania urządzenia IoT Plug and Play do certyfikacji.](tutorial-certification-test.md)
