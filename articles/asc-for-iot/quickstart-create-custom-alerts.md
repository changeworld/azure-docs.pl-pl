---
title: 'Szybki Start: tworzenie alertów niestandardowych dla Azure Security Center usługi IoT'
description: Informacje o tworzeniu i przypisywaniu niestandardowych alertów urządzeń dla Azure Security Center usługi zabezpieczeń IoT.
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
ms.openlocfilehash: 063e5c9e7d75fd1c07d148c265b1fe64eee3cbc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303532"
---
# <a name="quickstart-create-custom-alerts"></a>Szybki Start: tworzenie alertów niestandardowych


Korzystanie z niestandardowych grup zabezpieczeń i alertów pozwala w pełni wykorzystać kompleksowe informacje o zabezpieczeniach i kategoriie urządzenia, aby zapewnić lepsze zabezpieczenia rozwiązań IoT. 

## <a name="why-use-custom-alerts"></a>Dlaczego warto używać alertów niestandardowych? 

Wiesz, że Twoje urządzenia IoT są najlepsze.

W przypadku klientów, którzy w pełni rozumieją oczekiwane zachowanie urządzenia, Azure Security Center dla usługi IoT umożliwia przetłumaczenie tego zrozumienia na zasady zachowania urządzeń i alert dotyczący odchylenia od oczekiwanego, normalnego zachowania.

## <a name="security-groups"></a>Grupy zabezpieczeń

Grupy zabezpieczeń umożliwiają definiowanie logicznych grup urządzeń oraz zarządzanie ich stanem zabezpieczeń w scentralizowany sposób.

Te grupy mogą reprezentować urządzenia z określonym sprzętem, urządzeniami wdrożonymi w określonej lokalizacji lub dowolną inną grupą odpowiednią do konkretnych potrzeb.

Grupy zabezpieczeń są definiowane przez właściwość tag urządzenia o nazwie " **zabezpieczenia**". Domyślnie każde rozwiązanie IoT w IoT Hub ma jedną grupę zabezpieczeń o nazwie **default**. Zmień wartość właściwości Grupa **zabezpieczeń** , aby zmienić grupę zabezpieczeń urządzenia.
 
Na przykład:

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

Używanie grup zabezpieczeń do grupowania urządzeń w kategorie logiczne. Po utworzeniu grup Przypisz je do wybranych niestandardowych alertów, aby uzyskać najbardziej efektywne kompleksowe rozwiązanie zabezpieczeń IoT. 

## <a name="customize-an-alert"></a>Dostosowywanie alertu

1. Otwórz IoT Hub. 
2. Kliknij pozycję **alerty niestandardowe** w sekcji **zabezpieczenia** . 
3. Wybierz grupę zabezpieczeń, do której chcesz zastosować dostosowanie. 
4. Kliknij przycisk **Dodaj alert niestandardowy**.
5. Wybierz niestandardowy alert z listy rozwijanej. 
6. Edytuj wymagane właściwości, a następnie kliknij przycisk **OK**.
7. Pamiętaj, aby kliknąć przycisk **Zapisz**. Bez zapisywania nowego alertu alert zostanie usunięty przy następnym zamknięciu IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Alerty dostępne do dostosowania

Azure Security Center dla IoT oferuje dużą liczbę alertów, które można dostosować zgodnie z konkretnymi potrzebami. Zapoznaj się z [dostosowywalną tabelą alertów](concept-customizable-security-alerts.md) w celu uzyskania ważności alertu, źródła danych, opisu i naszych sugerowanych czynności zaradczych w przypadku odebrania każdego alertu. 


## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć agenta zabezpieczeń...

> [!div class="nextstepaction"]
> [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
