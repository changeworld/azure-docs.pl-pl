---
title: 'Samouczek: skalowanie aplikacji w chmurze Azure wiosennej | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skalować aplikację w chmurze Azure wiosennej na Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132884"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Samouczek: skalowanie aplikacji w chmurze Azure wiosennej

W tym samouczku przedstawiono sposób skalowania dowolnej aplikacji mikrousług przy użyciu pulpitu nawigacyjnego chmury Azure wiosny w Azure Portal. Skaluj swoją aplikację w górę i w dół, modyfikując liczbę wirtualnych procesorów CPU (procesorów wirtualnych vCPU) i ilość pamięci. Skaluj swoją aplikację w i na zewnątrz, modyfikując liczbę wystąpień aplikacji. Po zakończeniu dowiesz się, jak ręcznie wprowadzić ręczne korekty dla każdej aplikacji w usłudze. Skalowanie obowiązuje w kilka sekund i nie wymaga żadnych zmian w kodzie ani ponownego wdrożenia.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Wdrożone wystąpienie usługi w chmurze Azure wiosną.  Skorzystaj z naszego [przewodnika Szybki Start](spring-cloud-quickstart-launch-app-cli.md) , aby rozpocząć pracę.
* Co najmniej jedna aplikacja została już utworzona w tym wystąpieniu usługi.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Przejdź do strony skalowanie w Azure Portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do strony **omówienia** chmury Azure wiosennej.

1. Wybierz grupę zasobów, która zawiera tę usługę.

1. Przejdź do karty **aplikacje** pod nagłówkiem **Ustawienia** w menu po lewej stronie.

1. Wybierz aplikację, którą chcesz skalować. W tym przykładzie będziemy skalować aplikację o nazwie "Account-Service". Należy to zrobić na stronie **Przegląd** aplikacji.

1. Przejdź do karty **Skala** pod nagłówkiem **Ustawienia** w menu po lewej stronie. Powinny być widoczne opcje skalowania atrybutów pokazanych w poniższej sekcji.

## <a name="scale-your-application"></a>Skalowanie aplikacji

Atrybuty skalowania można modyfikować. Pamiętaj o następujących uwagach.

* **Procesory**: Maksymalna dozwolona liczba procesorów CPU wynosi 4 dla każdego wystąpienia aplikacji. Łączna liczba procesorów dla aplikacji to wartość ustawiona w tym obszarze pomnożona przez liczbę wystąpień aplikacji.

* **Pamięć/GB**: Maksymalna dozwolona ilość pamięci wynosi 8 GB na wystąpienie aplikacji.  Całkowita ilość pamięci dla aplikacji będzie wartością ustawioną w tym miejscu pomnożoną przez liczbę wystąpień aplikacji.

* **Liczba wystąpień aplikacji**: można skalować w poziomie do 20 wystąpień w warstwie Standardowa. Ta wartość zmienia liczbę oddzielnych uruchomionych wystąpień aplikacji mikrousług.

Kliknij przycisk **Zapisz** , aby zastosować ustawienia skalowania.

![Usługa skalowania w Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Po kilku sekundach wprowadzone zmiany zostaną wyświetlone na stronie **Przegląd** zawierającej więcej szczegółów dostępnych na karcie **wystąpienia aplikacji** . skalowanie nie wymaga żadnych zmian w kodzie ani ponownego wdrożenia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób ręcznego skalowania aplikacji w chmurze sieci platformy Azure.  Aby dowiedzieć się, jak monitorować aplikację, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak monitorować aplikację](spring-cloud-tutorial-distributed-tracing.md)
