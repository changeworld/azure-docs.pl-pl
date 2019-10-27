---
title: Zarządzanie aplikacją IoT Central platformy Azure | Microsoft Docs
description: Jako administrator, jak zarządzać aplikacją IoT Central platformy Azure, zmieniając nazwę aplikacji, adres URL, Przekaż obraz, skopiuj i Usuń aplikację
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4c729d2502464df021df4d03e989d489b2d1ecea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954593"
---
# <a name="manage-your-iot-central-application"></a>Zarządzanie aplikacją IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano, jak administrator może zarządzać aplikacją przez zmianę nazwy aplikacji i adresu URL, przekazanie obrazu, a także dowiedzieć się, jak kopiować i usuwać aplikacje w aplikacji IoT Central platformy Azure.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz mieć rolę **administratora** dla aplikacji IoT Central platformy Azure. Jeśli utworzysz aplikację IoT Central platformy Azure, zostanie ona automatycznie przypisana do roli **administratora** dla tej aplikacji. 

## <a name="change-application-name-and-url"></a>Zmień nazwę i adres URL aplikacji

Na stronie **Ustawienia aplikacji** można zmienić nazwę i adres URL aplikacji, a następnie wybrać pozycję **Zapisz**.

![Strona Ustawienia aplikacji](media/howto-administer/image0-a.png)

Jeśli administrator utworzy niestandardowy motyw dla aplikacji, ta strona zawiera opcję ukrycia **nazwy aplikacji** w interfejsie użytkownika. Jest to przydatne, jeśli logo aplikacji w motywie niestandardowym zawiera nazwę aplikacji. Aby uzyskać więcej informacji, zobacz [Dostosowywanie interfejsu użytkownika usługi Azure IoT Central](./howto-customize-ui.md).

> [!Note]
> Jeśli zmienisz adres URL, stary adres URL może zostać podjęty przez innego klienta IoT Central platformy Azure. Jeśli tak się stanie, nie jest już dostępny do użycia. Gdy zmienisz adres URL, stary adres URL nie będzie już działać i musisz powiadomić użytkowników o nowym adresie URL do użycia.

## <a name="prepare-and-upload-image"></a>Przygotowywanie i przekazywanie obrazu

Aby zmienić obraz aplikacji, zobacz [Przygotowywanie i przekazywanie obrazów do aplikacji IoT Central platformy Azure](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopiowanie aplikacji

Można utworzyć kopię dowolnej aplikacji, minus wszystkie wystąpienia urządzeń, historię danych urządzeń i dane użytkowników. Kopia jest aplikacją z płatność zgodnie z rzeczywistym użyciem, za którą zostanie naliczona opłata. W ten sposób nie można utworzyć aplikacji w wersji próbnej.

Wybierz pozycję **Kopiuj**. W oknie dialogowym Wprowadź szczegóły nowej aplikacji z opcją płatność zgodnie z rzeczywistym użyciem. Następnie wybierz pozycję **Kopiuj** , aby potwierdzić, że chcesz kontynuować. Dowiedz się więcej o polach w tym formularzu w temacie [Tworzenie aplikacji](quick-deploy-iot-central.md) — Szybki Start.

![Strona Ustawienia aplikacji](media/howto-administer/appcopy2.png)

Po pomyślnym zakończeniu operacji kopiowania aplikacji możesz przejść do nowej aplikacji za pomocą linku.

![Strona Ustawienia aplikacji](media/howto-administer/appcopy3a.png)

Kopiowanie aplikacji powoduje także skopiowanie definicji reguł i akcji poczty e-mail. Niektóre akcje, takie jak Flow, Logic Apps itp., są powiązane z określonymi regułami za pośrednictwem identyfikatora reguły. Gdy reguła jest kopiowana do innej aplikacji, pobiera swój własny identyfikator reguły. W takim przypadku użytkownicy będą musieli utworzyć nową akcję, a następnie skojarzyć z nią nową regułę. Ogólnie rzecz biorąc, warto sprawdzić reguły i akcje, aby upewnić się, że są one aktualne w nowej aplikacji.

> [!WARNING]
> Jeśli pulpit nawigacyjny zawiera kafelki, które wyświetlają informacje o określonych urządzeniach, wówczas te kafelki pokazują **żądany zasób nie został odnaleziony** w nowej aplikacji. Należy ponownie skonfigurować te kafelki, aby wyświetlić informacje o urządzeniach w nowej aplikacji.

## <a name="delete-an-application"></a>Usuwanie aplikacji

Aby trwale usunąć aplikację IoT Central, użyj przycisku **Usuń** . Ta akcja powoduje trwałe usunięcie wszystkich danych, które są skojarzone z aplikacją.

> [!Note]
> Aby usunąć aplikację, musisz mieć również uprawnienia do usuwania zasobów w ramach subskrypcji platformy Azure wybranej podczas tworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [Korzystanie z kontroli dostępu opartej na rolach w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Zarządzanie programowo

Pakiety SDK IoT Central Azure Resource Manager są dostępne dla węzłów, Python, C#Ruby, Java i go. Za pomocą tych pakietów można tworzyć, wyświetlać, aktualizować i usuwać IoT Central aplikacje. Pakiety obejmują pomocników do zarządzania uwierzytelnianiem i obsługą błędów.

Przykłady korzystania z zestawów SDK Azure Resource Manager można znaleźć w [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Aby dowiedzieć się więcej, zobacz następujące repozytoria i pakiety usługi GitHub:

| Język | Kopie | Pakiet |
| ---------| ---------- | ------- |
| Węzeł | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Następne kroki
 
Teraz, gdy wiesz już, jak administrować aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Zarządzanie użytkownikami i rolami](howto-manage-users-roles.md) w usłudze Azure IoT Central.