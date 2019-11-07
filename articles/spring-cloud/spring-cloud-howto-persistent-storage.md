---
title: Jak korzystać z magazynu trwałego w chmurze Azure wiosennej | Microsoft Docs
description: Jak korzystać z magazynu trwałego w chmurze Azure wiosennej
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: d70e7ff747b80b661e848f1c208f0d1c2c928248
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607770"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Jak korzystać z magazynu trwałego w chmurze Azure wiosennej

Chmura sprężynowa platformy Azure oferuje dwa typy magazynów dla aplikacji: trwałe i tymczasowe.  Chmura sprężynowa platformy Azure domyślnie umożliwia przechowywanie tymczasowe dla każdego wystąpienia aplikacji. Magazyn tymczasowy jest ograniczony do 5 GB z domyślną ścieżką instalacji: `/tmp`.

> [!WARNING]
> Ponowne uruchomienie wystąpienia aplikacji spowoduje trwałe usunięcie skojarzonego magazynu tymczasowego.

Magazyn trwały jest kontenerem udziałów plików zarządzanym przez platformę Azure przydzieloną na aplikację. Dane przechowywane w magazynie trwałym są współużytkowane przez wszystkie wystąpienia aplikacji. Wystąpienie usługi w chmurze Azure wiosennej może mieć maksymalnie 10 aplikacji z włączonym dyskiem trwałym. Każda aplikacja otrzymuje 50 GB magazynu trwałego. Domyślną ścieżką instalacji dla trwałego magazynu jest `/persistent`.

> [!WARNING]
> *Wyłączenie* trwałego magazynu spowoduje cofnięcie przydziału magazynu dla tej aplikacji.  Wszystkie dane na tym koncie magazynu zostaną utracone. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Włącz magazyn trwały przy użyciu Azure Portal

1. Na ekranie głównym Azure Portal wybierz pozycję **wszystkie zasoby**.

     >![Znajdź ikonę wszystkie zasoby](media/portal-all-resources.jpg)

1. Znajdź i wybierz zasób chmury Azure wiosny, który potrzebuje trwałego magazynu.  W tym przykładzie aplikacja jest nazywana *jpspring*.

    > ![Znajdź swój applicationb](media/select-service.jpg)

1. W polu Nagłówek **ustawień** wybierz pozycję **aplikacje**.

1. Usługi w chmurze wiosny będą wyświetlane w tabeli.  Wybierz usługę, do której chcesz dodać magazyn trwały.  W tym przykładzie wybierzemy naszą usługę **bramy** .

    > ![Wybierz swoją usługę](media/select-gateway.jpg)

1. W bloku konfiguracja usługi wybierz pozycję **Konfiguracja** .

1. Wybierz kartę **Magazyn trwały** i Włącz trwały magazyn.

    > ![Włącz magazyn trwały](media/enable-persistent-storage.jpg)

Gdy magazyn trwały jest włączony, jego rozmiar i ścieżka są wyświetlane na tej stronie.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Modyfikowanie trwałego magazynu za pomocą interfejsu wiersza polecenia platformy Azure

W razie potrzeby zainstaluj rozszerzenie chmury wiosennej dla interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name spring-cloud
```

Utwórz aplikację z włączonym dyskiem trwałym:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Włącz magazyn trwały w istniejącej aplikacji:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

Wyłącz magazyn trwały w istniejącej aplikacji:

> [!WARNING]
> Wyłączenie trwałego magazynu spowoduje cofnięcie przydziału magazynu dla tej aplikacji, a na stałe utratę wszelkich przechowywanych tam danych. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przydziałów aplikacji i usług](spring-cloud-quotas.md)lub Dowiedz się, jak [ręcznie skalować aplikację](spring-cloud-tutorial-scale-manual.md).