---
title: Biblioteki IoT Plug and Play i SDK
description: Informacje o bibliotekach urządzeń i usług dostępnych do tworzenia rozwiązań obsługujących technologię IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064325"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Biblioteki IoT Plug and Play i SDK

Biblioteki IoT Plug and Play i zestawY SDK umożliwiają deweloperom tworzenie rozwiązań IoT przy użyciu różnych języków programowania na wielu platformach. Poniższa tabela zawiera łącza do przykładów i przewodników Szybki start, które ułatwią rozpoczęcie pracy:

## <a name="microsoft-supported-libraries-and-sdks"></a>Biblioteki i sdk obsługiwane przez firmę Microsoft

| Platforma | Biblioteka/pakiet | Kod źródłowy | Sample | Szybki start | Tematy pomocy |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [SDK urządzenia na apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Próbki klienta Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Tematy pomocy](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [SDK urządzenia na Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Próbki klienta Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Tematy pomocy](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [SDK urządzenia na EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Próbki klienta Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Tematy pomocy](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [SDK urządzenia w Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Próbki klienta Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Tematy pomocy](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [SDK urządzenia na CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Próbki klienta Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Tematy pomocy](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Cyfrowe próbki bliźniacze](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Tematy pomocy](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Cyfrowe próbki bliźniacze](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-java.md) | [Tematy pomocy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [Npm](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Cyfrowe próbki bliźniacze](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-node.md) | [Tematy pomocy](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Obsługa usługi IoT Hub

Funkcje urządzeń IoT Plug and Play są obsługiwane tylko przez [bezpłatne i standardowe koncentratory IoT.](../iot-hub/iot-hub-scaling.md)

## <a name="next-steps"></a>Następne kroki

Oprócz zestawów SDK urządzeń i bibliotek można używać interfejsów API REST do interakcji z repozytoriami modelu.