---
title: 'Szybki start: tworzenie alertów niestandardowych'
description: Opis, tworzenie i przypisywanie alertów urządzeń niestandardowych dla usługi zabezpieczeń Azure Security Center dla usługi zabezpieczeń IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 8ba81b669c25d91efa8ad1f07b1a7835b42c5cf4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310828"
---
# <a name="quickstart-create-custom-alerts"></a>Szybki start: tworzenie alertów niestandardowych

Korzystając z niestandardowych grup zabezpieczeń i alertów, w pełni wykorzystuje kompleksowe informacje o zabezpieczeniach i wiedzę na temat urządzeń kategorycznych, aby zapewnić lepsze bezpieczeństwo w rozwiązaniu IoT.

## <a name="why-use-custom-alerts"></a>Dlaczego warto korzystać z alertów niestandardowych?

Najlepiej znasz swoje urządzenia IoT.

Dla klientów, którzy w pełni rozumieją swoje oczekiwane zachowanie urządzenia, Usługa Azure Security Center dla IoT umożliwia tłumaczenie tego zrozumienia na zasady zachowania urządzenia i alerty dotyczące wszelkich odchyleń od oczekiwanego, normalnego zachowania.

## <a name="security-groups"></a>Grupy zabezpieczeń

Grupy zabezpieczeń umożliwiają definiowanie grup logicznych urządzeń i zarządzanie ich stanem zabezpieczeń w sposób scentralizowany.

Te grupy mogą reprezentować urządzenia z określonym sprzętem, urządzeniami wdrożonymi w określonej lokalizacji lub dowolną inną grupą odpowiednią do określonych potrzeb.

Grupy zabezpieczeń są definiowane przez właściwość tagu bliźniaczej reprezentacji urządzenia o nazwie **SecurityGroup**. Domyślnie każde rozwiązanie IoT w Centrum IoT hub ma jedną grupę zabezpieczeń o nazwie **domyślnie**. Zmień wartość właściwości **SecurityGroup,** aby zmienić grupę zabezpieczeń urządzenia.

Przykład:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Grupy zabezpieczeń używają do grup dyskusyjnych, aby pogrupować urządzenia w kategorie logiczne. Po utworzeniu grup przypisz je do wybranych alertów niestandardowych, aby uzyskać najskuteczniejsze kompleksowe rozwiązanie zabezpieczające IoT.

## <a name="customize-an-alert"></a>Dostosowywanie alertu

1. Otwórz centrum IoT Hub.
1. Kliknij **pozycję Alerty niestandardowe** w sekcji **Zabezpieczenia.**
1. Wybierz grupę zabezpieczeń, do której chcesz zastosować dostosowanie.
1. Kliknij **pozycję Dodaj alert niestandardowy**.
1. Wybierz niestandardowy alert z listy rozwijanej.
1. Edytuj wymagane właściwości, kliknij przycisk **OK**.
1. Pamiętaj, aby kliknąć przycisk **ZAPISZ**. Bez zapisywania nowego alertu alert zostanie usunięty przy następnym zamknięciu usługi IoT Hub.

## <a name="alerts-available-for-customization"></a>Alerty dostępne do dostosowywania

Usługa Azure Security Center for IoT oferuje dużą liczbę alertów, które można dostosować do określonych potrzeb. Przejrzyj [dostosowywaną tabelę alertów,](concept-customizable-security-alerts.md) aby uzyskać ważność alertów, źródło danych, opis i sugerowane kroki korygowania, jeśli i kiedy zostanie odebrany każdy alert.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć agenta zabezpieczeń...

> [!div class="nextstepaction"]
> [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
