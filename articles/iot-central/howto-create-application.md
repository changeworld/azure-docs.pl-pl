---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Jako administratora jak utworzyć aplikację usługi Azure IoT Central.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 1fbe3ea142e1dd738cd341f57d2b8f48b539ac75
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003770"
---
# <a name="create-your-azure-iot-central-application"></a>Tworzenie aplikacji usługi Azure IoT Central

Tworzenie aplikacji systemu Microsoft Azure IoT Central [Utwórz aplikację](https://apps.microsoftiotcentral.com/create) strony. Aby utworzyć aplikację usługi Azure IoT Central, musisz wypełnić wszystkie pola na tej stronie, a następnie wybierz **Utwórz**. Przekonasz się, zawiera więcej informacji na temat każdego z poniższych pól.

![Tworzenie strony aplikacji](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Plan płatności

Można utworzyć wersji próbnej lub płatnej aplikacji. Dowiedz się więcej o aplikacjach płatnych oraz wersji próbnych na [stronę z cennikiem usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/)...

## <a name="application-name"></a>Nazwa aplikacji

Nazwa aplikacji jest wyświetlana na **Menedżer aplikacji** strony i w ramach każdej aplikacji usługi Azure IoT Central. Możesz wybrać dowolną nazwę aplikacji usługi Azure IoT Central. Wybierz nazwę, która ma sens dla Ciebie i innym osobom w organizacji.

## <a name="application-url"></a>Adres URL aplikacji

Adres URL aplikacji jest link do aplikacji. Można zapisać zakładki do niego w przeglądarce lub udostępnić go innym osobom.

Po wprowadzeniu nazwy aplikacji, adres URL aplikacji jest generowany automatycznie. Jeśli wolisz, możesz inny adres URL aplikacji. Każdy Azure IoT Central adres URL musi być unikatowa w obrębie usługi Azure IoT Central. Zobaczysz komunikat o błędzie, jeśli wybierzesz adres URL jest już zajęta.

## <a name="directory"></a>Katalog

Tylko w aplikacje płatne.

Wybierz dzierżawę usługi Azure Active Directory, aby utworzyć aplikację usługi Azure IoT Central. Dzierżawy usługi Azure Active Directory zawiera tożsamości użytkownika, poświadczeń i innych informacji o organizacji. Wiele subskrypcji platformy Azure może być skojarzony z pojedynczej dzierżawy usługi Azure Active Directory.

Jeśli nie masz dzierżawę usługi Azure Active Directory, utworzony automatycznie podczas tworzenia subskrypcji platformy Azure.

Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Subskrypcja platformy Azure umożliwia tworzenie wystąpienia usług platformy Azure. Azure IoT Central automatycznie znajdzie wszystkie subskrypcje platformy Azure, masz dostęp do, a następnie wyświetli je na liście rozwijanej na **Utwórz aplikację** strony. Wybierz subskrypcję platformy Azure, aby utworzyć nową aplikację Central IoT Azure.

Jeśli nie masz subskrypcji platformy Azure, można utworzyć jeden na [strona tworzenia konta usługi Azure](https://aka.ms/createazuresubscription). Po utworzeniu subskrypcji platformy Azure, przejdź z powrotem do **Utwórz aplikację** strony. Nowej subskrypcji jest wyświetlana w **subskrypcji platformy Azure** listy rozwijanej.

Aby dowiedzieć się więcej, zobacz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Tylko w aplikacje płatne.

Wybierz region, w którym chcesz utworzyć aplikację usługi Azure IoT centralnej. Zazwyczaj należy wybrać region znajdujący się najbliżej fizycznie urządzenia, aby uzyskać optymalną wydajność.

Aby dowiedzieć się więcej, zobacz [regionów świadczenia usługi Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Możesz zobaczyć regionów, w których usługa Azure IoT Central jest dostępna na [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/) strony.

> [!Note]
> Po wybraniu regionu, nie możesz później przenieść aplikację do innego regionu.

## <a name="application-template"></a>Szablon aplikacji

Można wybrać jedną z szablonów aplikacji dostępnych w nowej aplikacji usługi Azure IoT Central. Szablon aplikacji może zawierać wstępnie zdefiniowane elementy, takie jak szablony urządzenia i pulpity nawigacyjne, aby pomóc Ci rozpocząć pracę.

| Szablon aplikacji | Opis |
| -------------------- | ----------- |
| Aplikacji niestandardowej   | Tworzy pustą aplikację dla Ciebie zostać wypełniony przy użyciu własnych urządzeń i urządzeń. |
| Przykładowe firmy Contoso       | Tworzy aplikację, która zawiera szablon urządzenia dla prostych podłączonego urządzenia. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
| Przykładowe Devkits       | Tworzy aplikację za pomocą szablonów urządzenia gotowe do podłączenia urządzenia zestawu deweloperskiego lub urządzenia Raspberry Pi. Użyj tego szablonu, jeśli jesteś deweloperem urządzenia, eksperymentowanie z kodem na jednym z tych urządzeń. |

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak utworzyć aplikację usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)