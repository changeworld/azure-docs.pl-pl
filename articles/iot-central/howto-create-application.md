---
title: Tworzenie aplikacji usługi Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Jako administratora jak utworzyć aplikację Azure IoT centralnej.
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 83879c6b81985f61b9fcff665e9f764c1346592e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-your-azure-iot-central-application"></a>Tworzenie aplikacji platformy Azure IoT środkowe

Tworzenie aplikacji Microsoft Azure IoT centralnej [tworzenie aplikacji](https://apps.microsoftiotcentral.com/create) strony. Aby utworzyć aplikację Azure IoT centralnej, musi wykonać wszystkie pola na tej stronie, a następnie wybierz pozycję **Utwórz**. Ten artykuł zawiera więcej informacji o poszczególnych pól.

![Tworzenie strony aplikacji](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Plan płatności

Można utworzyć wersji próbnej lub płatnej aplikacji. Dowiedz się więcej o aplikacjach płatnych oraz wersji próbnych na tej stronie.

## <a name="application-name"></a>Nazwa aplikacji

Nazwa aplikacji jest wyświetlana na **Menedżer aplikacji** strony i w obrębie każdej aplikacji Azure IoT centralnej. Można wybrać dowolną nazwę aplikacji usługi Azure IoT centralnej. Wybierz nazwę, która ma sens dla Ciebie i do innych użytkowników w organizacji.

## <a name="application-url"></a>Adres URL aplikacji

Adres URL aplikacji jest łącze do aplikacji. Można zapisać zakładki w przeglądarce lub udostępniać innym osobom.

Po wprowadzeniu nazwy aplikacji, adres URL aplikacji został wygenerowany automatycznie. Jeśli wolisz, możesz wybrać inny adres URL aplikacji. Każdy Azure IoT centralnej adres URL musi być unikatowa. Zostanie wyświetlony komunikat o błędzie, jeśli wybierzesz adres URL jest już zajęta.

## <a name="directory"></a>Katalog

Tylko w aplikacjach płatną.

Wybierz dzierżawy usługi Azure Active Directory, aby utworzyć aplikację Azure IoT centralnej. Dzierżawy usługi Azure Active Directory zawiera tożsamości użytkownika, poświadczeń i innych informacji organizacyjnych. Wiele subskrypcji Azure może być skojarzony z pojedynczej dzierżawy usługi Azure Active Directory.

Jeśli nie masz dzierżawę usługi Azure Active Directory, jest on stworzony dla Ciebie podczas tworzenia subskrypcji platformy Azure.

Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Subskrypcja platformy Azure umożliwia tworzenie wystąpień usług platformy Azure. Azure IoT centralnego automatycznie wyszukuje wszystkie subskrypcje platformy Azure, masz dostęp do i wyświetla je na liście rozwijanej na **tworzenie aplikacji** strony. Wybierz subskrypcję platformy Azure, aby utworzyć nową aplikację centralnej IoT Azure.

Jeśli nie masz subskrypcji platformy Azure, można utworzyć jedną na tej stronie. Po utworzeniu subskrypcji platformy Azure, przejdź wstecz do **tworzenie aplikacji** strony. Nowej subskrypcji zostanie wyświetlony w **subskrypcji Azure** listy rozwijanej.

Aby dowiedzieć się więcej, zobacz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Tylko w aplikacjach płatną.

Wybierz region, w którym chcesz utworzyć aplikację Azure IoT centralnej. Zazwyczaj należy wybrać region najbliższy fizycznie urządzenia, aby uzyskać optymalną wydajność.

Aby dowiedzieć się więcej, zobacz [regiony platformy Azure](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions).

Zostanie wyświetlony regionów, w których Azure IoT centralnej jest dostępna na [produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/) strony.

> [!Note]
> Po wybraniu regionu później nie można przenieść aplikację w innym regionie.

## <a name="application-template"></a>Szablon aplikacji

Można wybrać jeden z szablonów aplikacji dostępnych dla nowej aplikacji Azure IoT centralnej. Szablon aplikacji może zawierać wstępnie zdefiniowane elementy, takie jak szablony urządzenia i pulpity nawigacyjne ułatwiające rozpoczęcie pracy:

| Szablon aplikacji | Opis |
| -------------------- | ----------- |
| Aplikacji niestandardowych   | Tworzy pustą aplikację można wypełnić przy użyciu własnych urządzeń i szablony urządzenia. |
| Przykładowe firmy Contoso       | Tworzy aplikację, która zawiera szablon urządzenia dla prostego podłączonego urządzenia. Aby rozpocząć eksplorowanie usługi Azure IoT centralną, należy użyć tego szablonu. |
| Devkits próbki       | Tworzy aplikację z szablonami urządzenia gotowe do Podłącz urządzenie z systemem MXChip lub malina Pi. Jeśli jesteś deweloperem urządzenia eksperymentowanie z kodem na jednym z tych urządzeń, należy użyć tego szablonu. |

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak utworzyć aplikację Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Administrowanie aplikacji](howto-administer.md)