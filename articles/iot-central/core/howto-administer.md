---
title: Zmienianie ustawień aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Jak zarządzać aplikacją Azure IoT Central, zmieniając nazwę aplikacji, adres URL, obraz przekazywania i usuwanie aplikacji jako administratora.
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158689"
---
# <a name="change-iot-central-application-settings"></a>Zmienianie ustawień aplikacji IoT Central



W tym artykule opisano, jak jako administrator możesz zarządzać aplikacją, zmieniając nazwę i adres URL aplikacji, przekazując obraz i usuwając aplikację w aplikacji Azure IoT Central.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz być w roli **administratora** dla aplikacji Azure IoT Central. Jeśli tworzysz aplikację Azure IoT Central, automatycznie zostaniesz przypisany do roli **administratora** dla tej aplikacji.

## <a name="change-application-name-and-url"></a>Zmienianie nazwy i adresu URL aplikacji

Na stronie **Ustawienia aplikacji** możesz zmienić nazwę i adres URL aplikacji, a następnie wybrać pozycję **Zapisz**.

![Strona ustawień aplikacji](media/howto-administer/image0-a.png)

Jeśli administrator utworzy niestandardowy motyw dla aplikacji, ta strona zawiera opcję ukrycia **nazwy aplikacji** w interfejsie użytkownika. Ta opcja jest przydatna, jeśli logo aplikacji w motywie niestandardowym zawiera nazwę aplikacji. Aby uzyskać więcej informacji, zobacz [Dostosowywanie interfejsu użytkownika centrali Usługi Azure IoT](./howto-customize-ui.md).

> [!Note]
> Jeśli zmienisz adres URL, stary adres URL może zostać przejęty przez innego klienta usługi Azure IoT Central. Jeśli tak się stanie, nie jest już dostępny do użycia. Gdy zmienisz adres URL, stary adres URL przestanie działać i musisz powiadomić użytkowników o nowym adresie URL, który ma być używany.

## <a name="delete-an-application"></a>Usuwanie aplikacji

Użyj przycisku **Usuń,** aby trwale usunąć aplikację IoT Central. Ta akcja trwale usuwa wszystkie dane skojarzone z aplikacją.

> [!Note]
> Aby usunąć aplikację, musisz mieć również uprawnienia do usuwania zasobów w subskrypcji platformy Azure wybranej podczas tworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą kontroli dostępu opartej na rolach.](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)

## <a name="manage-programmatically"></a>Zarządzanie programistyczne

Pakiety SDK usługi IoT Central Azure Resource Manager są dostępne dla node, Python, C#, Ruby, Java i Go. Za pomocą tych pakietów można tworzyć, listy, aktualizować lub usuwać aplikacje IoT Central. Pakiety obejmują pomocników do zarządzania uwierzytelnianiem i obsługą błędów.

Przykłady używania zestawów SDK usługi Azure Resource [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)Manager można znaleźć pod adresem .

Aby dowiedzieć się więcej, zobacz następujące repozytoria i pakiety GitHub:

| Język | Repozytorium | Pakiet |
| ---------| ---------- | ------- |
| Węzeł | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Przejdź | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiesz się, jak administrować aplikacją Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się [z zarządzaniem użytkownikami i rolami](howto-manage-users-roles.md) w usłudze Azure IoT Central.
