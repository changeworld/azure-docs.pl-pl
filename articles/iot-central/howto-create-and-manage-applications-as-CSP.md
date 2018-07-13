---
title: Tworzenie i zarządzanie aplikacjami usługi Azure IoT Central jako dostawca CSP | Dokumentacja firmy Microsoft
description: Jako dostawca CSP jak utworzyć aplikację usługi Azure IoT Central w imieniu klienta.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: d32e05e99543b30ee92ea455ae2f800b09d83661
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009294"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>Jako dostawca CSP tworzenie i zarządzanie aplikację Central IoT platformy Azure w imieniu klienta 

Program Microsoft Cloud Solution Provider (CSP) to program Reseller firmy Microsoft. Jego celem jest zapewnienie naszych partnerów handlowych w programie, kompleksowy do odsprzedaży platformy wszystkich komercyjnych usług Online firmy Microsoft. Dowiedz się więcej o [programu Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Jako dostawca CSP, można tworzyć i zarządzać aplikacjami Microsoft Azure IoT Central w imieniu klientów za pośrednictwem [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Gdy aplikacje usługi Azure IoT Central są tworzone w imieniu klientów przez dostawców usług kryptograficznych, podobnie jak z innymi dostawcy usług Kryptograficznych zarządzanych usług platformy Azure, CSP zarządzają rozliczeniami dla klientów. Opłaty usługi Azure IoT Central pojawi się rachunek w programie Microsoft Partner Center.

Aby rozpocząć, zaloguj się na swoje konto w portalu dla partnerów firmy Microsoft i wybierz klienta, dla którego chcesz utworzyć aplikację usługi Azure IoT Central. Przejdź do zarządzania usługami dla klienta w lewym okienku nawigacji

![Program Microsoft Partner Center, widoku klienta](media\howto-create-application-asCSP\image1.png)

Usługa Azure IoT Central jest wyświetlany jako usługa dostępne do administrowania. Kliknij link usługi Azure IoT Central, na stronie, aby tworzyć nowe aplikacje i Zarządzaj istniejących aplikacji dla danego klienta.

![Usługa Azure IoT Central dostępne na potrzeby zarządzania](media\howto-create-application-asCSP\image2.png)

Zostanie wyświetlony na stronie usługi Azure IoT Central aplikacji Manager. Azure IoT Central utrzymuje kontekst pochodzi Center partnera firmy Microsoft i przejście do zarządzania tego określonego klienta. Zostanie wyświetlony to potwierdzone w nagłówku strony Menedżer aplikacji. W tym miejscu możesz przejść do istniejącej aplikacji została wcześniej utworzona dla danego klienta do zarządzania serwerem lub Utwórz nową aplikację klienta.

![Tworzenie menedżera dla dostawców CSP](media\howto-create-application-asCSP\image3.png)

Aby utworzyć aplikację usługi Azure IoT Central, kliknij przycisk **nową aplikację** kafelka. Spowoduje to załadowanie strony tworzenia aplikacji. Musisz wypełnić wszystkie pola na tej stronie, a następnie wybierz **Utwórz**. Możesz znaleźć więcej informacji na temat każdego z poniższych pól.

![Tworzenie strony aplikacji dla dostawców CSP](media\howto-create-application-asCSP\image4-1.png)

![Tworzenie strony aplikacji dla dostawców CSP](media\howto-create-application-asCSP\image4-2.png)

## <a name="payment-plan"></a>Plan płatności

Można tworzyć tylko tworzyć aplikacje płatne jako dostawca CSP. Aby zaprezentować usługi Azure IoT Central do klienta, można utworzyć aplikację wersji próbnej oddzielnie. Dowiedz się więcej o aplikacjach płatnych oraz wersji próbnych na [stronę z cennikiem usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nazwa aplikacji

Nazwa aplikacji jest wyświetlana na **Menedżer aplikacji** strony i w ramach każdej aplikacji usługi Azure IoT Central. Możesz wybrać dowolną nazwę aplikacji usługi Azure IoT Central. Wybierz nazwę, która ma sens dla Ciebie i innym osobom w organizacji.

## <a name="application-url"></a>Adres URL aplikacji

Adres URL aplikacji jest link do aplikacji. Można zapisać zakładki do niego w przeglądarce lub udostępnić go innym osobom.

Po wprowadzeniu nazwy aplikacji, adres URL aplikacji jest generowany automatycznie. Jeśli wolisz, możesz inny adres URL aplikacji. Każdy Azure IoT Central adres URL musi być unikatowa w obrębie usługi Azure IoT Central. Zobaczysz komunikat o błędzie, jeśli wybierzesz adres URL jest już zajęta.

## <a name="directory"></a>Katalog

Ponieważ usługa Azure IoT Central kontekstu, dołączone do zarządzania klienta, który został wybrany w portalu dla partnerów firmy Microsoft, zostanie wyświetlony tylko dzierżawy usługi Azure Active Directory dla tego klienta, w polu katalogu. 

Dzierżawy usługi Azure Active Directory zawiera tożsamości użytkownika, poświadczeń i innych informacji o organizacji. Wiele subskrypcji platformy Azure może być skojarzony z pojedynczej dzierżawy usługi Azure Active Directory.

Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Subskrypcja platformy Azure umożliwia tworzenie wystąpienia usług platformy Azure. Azure IoT Central automatycznie znajdzie wszystkie subskrypcje platformy Azure, odbiorcy, do których masz dostęp, a następnie wyświetli je na liście rozwijanej na **Utwórz aplikację** strony. Wybierz subskrypcję platformy Azure, aby utworzyć nową aplikację Central IoT Azure.

Jeśli nie masz subskrypcji platformy Azure, możesz go utworzyć w programie Microsoft Partner Center. Po utworzeniu subskrypcji platformy Azure, przejdź z powrotem do **Utwórz aplikację** strony. Nowej subskrypcji jest wyświetlana w **subskrypcji platformy Azure** listy rozwijanej.

Aby dowiedzieć się więcej, zobacz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

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

Teraz, gdy wiesz jak utworzyć aplikację usługi Azure IoT Central jako dostawcy usług Kryptograficznych, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)