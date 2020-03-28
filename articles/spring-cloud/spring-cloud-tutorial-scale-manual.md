---
title: 'Samouczek: Skalowanie aplikacji w wiosennej chmurze platformy Azure | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skalować aplikację za pomocą usługi Azure Spring Cloud w witrynie Azure portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277480"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Skalowanie aplikacji w chmurze Azure Spring Cloud

W tym samouczku pokazano, jak skalować dowolną aplikację mikrousług przy użyciu pulpitu nawigacyjnego usługi Azure Spring Cloud w witrynie Azure portal.

Skaluj aplikację w górę i w dół, modyfikując liczbę procesorów wirtualnych (vCPU) i ilość pamięci. Skalowanie aplikacji w i na zewnątrz, modyfikując liczbę wystąpień aplikacji.

Po zakończeniu dowiesz się, jak wprowadzać szybkie ręczne zmiany w każdej aplikacji w usłudze. Skalowanie staje się skuteczne w ciągu kilku sekund i nie wymaga żadnych zmian kodu lub ponownego rozmieszczenia.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 
* Wdrożone wystąpienie usługi Azure Spring Cloud.  Postępuj zgodnie z naszym [przewodnikiem Szybki start podczas wdrażania aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure,](spring-cloud-quickstart-launch-app-cli.md) aby rozpocząć.
* Co najmniej jedna aplikacja już utworzona w wystąpieniu usługi.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Przechodzenie do strony Skalowanie w witrynie Azure Portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Przejdź do strony **Przegląd chmury** usługi Azure Spring.

1. Wybierz grupę zasobów zawierającą usługę.

1. Wybierz kartę **Aplikacje** w obszarze **Ustawienia** w menu po lewej stronie.

1. Wybierz aplikację, którą chcesz skalować. W tym przykładzie wybierz aplikację o nazwie **account-service**. Następnie należy wyświetlić stronę **Przegląd** aplikacji.

1. Przejdź do karty **Skaluj** w obszarze **Ustawienia** w menu po lewej stronie. Powinny być widoczne opcje skalowania atrybutów pokazanych w poniższej sekcji.

## <a name="scale-your-application"></a>Skalowanie aplikacji

Jeśli zmodyfikujesz atrybuty skalowania, należy pamiętać o następujących uwagach:

* **Procesory:** Maksymalna liczba procesorów na wystąpienie aplikacji wynosi cztery. Całkowita liczba procesorów dla aplikacji jest wartością ustawioną w tym miejscu pomnożoną przez liczbę wystąpień aplikacji.

* **Pamięć/GB**: Maksymalna ilość pamięci na wystąpienie aplikacji wynosi 8 GB. Całkowita ilość pamięci dla aplikacji jest wartością ustawioną w tym miejscu pomnożoną przez liczbę wystąpień aplikacji.

* **Liczba wystąpień aplikacji:** w warstwie Standardowa można skalować w poziomie do maksymalnie 20 wystąpień. Ta wartość zmienia liczbę oddzielnych uruchomionych wystąpień aplikacji mikrousług.

Pamiętaj, aby wybrać **pozycję Zapisz,** aby zastosować ustawienia skalowania.

![Usługa Skalowanie w witrynie Azure portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Po kilku sekundach wprowadzone zmiany są wyświetlane na stronie **Przegląd,** a więcej szczegółów jest dostępnych na karcie **Wystąpienia aplikacji.**

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak ręcznie skalować aplikacje usługi Azure Spring Cloud. Aby dowiedzieć się, jak monitorować aplikację, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak monitorować aplikację](spring-cloud-tutorial-distributed-tracing.md)
