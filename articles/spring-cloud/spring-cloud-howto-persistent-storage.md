---
title: Jak korzystać z magazynu trwałego w chmurze Azure wiosennej | Microsoft Docs
description: Jak korzystać z magazynu trwałego w chmurze Azure wiosennej
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039091"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Jak korzystać z magazynu trwałego w chmurze Azure wiosennej

Chmura sprężynowa platformy Azure oferuje dwa typy magazynów dla aplikacji: trwałe i tymczasowe.  Chmura sprężynowa platformy Azure domyślnie umożliwia przechowywanie tymczasowe dla każdego wystąpienia aplikacji. Magazyn tymczasowy jest ograniczony do 5 GB, a jego domyślna ścieżka instalacji to `/tmp`.

> [!WARNING]
> Ponowne uruchomienie wystąpienia aplikacji spowoduje trwałe usunięcie skojarzonego magazynu tymczasowego.

Magazyn trwały jest kontenerem udziałów plików zarządzanym przez platformę Azure, który jest przydzielony do zakresu poszczególnych aplikacji. Dane przechowywane w magazynie trwałym są współużytkowane przez wszystkie wystąpienia aplikacji. Wystąpienie usługi w chmurze Azure wiosennej może mieć maksymalnie 10 aplikacji z włączonym dyskiem trwałym, a każda aplikacja ma 50 GB magazynu trwałego. Domyślna ścieżka instalacji dla trwałego magazynu to `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Włącz magazyn trwały przy użyciu Azure Portal

1. Na stronie usługi w chmurze Azure wiosny wybierz pozycję **pulpit nawigacyjny aplikacji**, a następnie wybierz aplikację wymagającą trwałego magazynu.
1. Na karcie **Omówienie** Znajdź atrybut **Magazyn trwały** i wybierz pozycję **wyłączone**.
1. Kliknij pozycję **Włącz** , aby włączyć magazyn trwały, a następnie wybierz przycisk **OK** , aby zastosować zmianę.

Gdy magazyn trwały jest włączony, jego rozmiar i ścieżka są wyświetlane w atrybucie **Magazyn trwały** na stronie **Przegląd** .

> [!WARNING]
> *Wyłączenie* trwałego magazynu spowoduje cofnięcie przydziału magazynu dla tej aplikacji.  Wszystkie dane na tym koncie magazynu zostaną utracone. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Włącz magazyn trwały przy użyciu interfejsu wiersza polecenia platformy Azure

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