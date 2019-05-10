---
title: Tworzenie i zarządzanie aplikacjami usługi Azure IoT Central jako dostawca CSP | Dokumentacja firmy Microsoft
description: Jako dostawca CSP jak utworzyć aplikację usługi Azure IoT Central w imieniu klienta.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/03/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 43fe0047022173cc20f58688920fa95b95cc6c22
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473539"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>Jako dostawca CSP tworzenie i zarządzanie aplikację Central IoT platformy Azure w imieniu klienta 

Program Microsoft Cloud Solution Provider (CSP) to program Reseller firmy Microsoft. Jego celem jest zapewnienie naszych partnerów handlowych w programie, kompleksowy do odsprzedaży platformy wszystkich komercyjnych usług Online firmy Microsoft. Dowiedz się więcej o [programu Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Jako dostawca CSP, można tworzyć i zarządzać aplikacjami Microsoft Azure IoT Central w imieniu klientów za pośrednictwem [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Gdy aplikacje usługi Azure IoT Central są tworzone w imieniu klientów przez dostawców usług kryptograficznych, podobnie jak z innymi dostawcy usług Kryptograficznych zarządzanych usług platformy Azure, CSP zarządzają rozliczeniami dla klientów. Opłaty usługi Azure IoT Central pojawi się na rachunek w programie Microsoft Partner Center.

Aby rozpocząć, zaloguj się na swoje konto w portalu dla partnerów firmy Microsoft i wybierz klienta, dla którego chcesz utworzyć aplikację usługi Azure IoT Central. Przejdź do zarządzania usługami dla klienta w lewym okienku nawigacji

![Program Microsoft Partner Center, widoku klienta](media/howto-create-application-csp/image1.png)

Usługa Azure IoT Central jest wyświetlany jako usługa dostępne do administrowania. Wybierz link usługi Azure IoT Central, na stronie, aby tworzyć nowe aplikacje i Zarządzaj istniejących aplikacji dla danego klienta.

![Usługa Azure IoT Central dostępne na potrzeby zarządzania](media/howto-create-application-csp/image2.png)

Zostanie wyświetlony na stronie usługi Azure IoT Central aplikacji Manager. Azure IoT Central utrzymuje kontekst pochodzi Center partnera firmy Microsoft i przejście do zarządzania tego określonego klienta. Zostanie wyświetlony to potwierdzone w nagłówku strony Menedżer aplikacji. W tym miejscu można albo przejść do istniejącej aplikacji utworzonej miał wcześniej dla tego klienta do zarządzania lub Utwórz nową aplikację klienta.

![Tworzenie menedżera dla dostawców CSP](media/howto-create-application-csp/image3.png)

Aby utworzyć aplikację usługi Azure IoT Central, wybierz pozycję **nową aplikację** kafelka. Spowoduje to załadowanie strony tworzenia aplikacji. Musisz wypełnić wszystkie pola na tej stronie, a następnie wybierz **Utwórz**. Możesz znaleźć więcej informacji na temat każdego z poniższych pól.

![Tworzenie strony aplikacji dla dostawców CSP](media/howto-create-application-csp/image4.png)

![Tworzenie strony aplikacji dla dostawców CSP](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>Plan płatności

Płatność za rzeczywiste użycie aplikacji można utworzyć tylko jako dostawca CSP. Aby zaprezentować usługi Azure IoT Central do klienta, można utworzyć aplikację wersji próbnej oddzielnie. Dowiedz się więcej na temat aplikacji do udostępniania wersji próbnych i płatność za rzeczywiste użycie na [stronę z cennikiem usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nazwa aplikacji

Nazwa aplikacji jest wyświetlana na **Menedżer aplikacji** strony i w ramach każdej aplikacji usługi Azure IoT Central. Możesz wybrać dowolną nazwę aplikacji usługi Azure IoT Central. Wybierz nazwę, która ma sens dla Ciebie i innym osobom w organizacji.

## <a name="application-url"></a>Adres URL aplikacji

Adres URL aplikacji jest link do aplikacji. Można zapisać zakładki do niego w przeglądarce lub udostępnić go innym osobom.

Po wprowadzeniu nazwy aplikacji, adres URL aplikacji jest generowana automatycznie. Jeśli wolisz, możesz inny adres URL aplikacji. Każdy Azure IoT Central adres URL musi być unikatowa w obrębie usługi Azure IoT Central. Zobaczysz komunikat o błędzie, jeśli wybierzesz adres URL jest już zajęta.

## <a name="directory"></a>Katalog

Ponieważ usługa Azure IoT Central kontekstu, dołączone do zarządzania klienta, który został wybrany w portalu dla partnerów firmy Microsoft, zostanie wyświetlony tylko dzierżawy usługi Azure Active Directory dla tego klienta, w polu katalogu. 

Dzierżawy usługi Azure Active Directory zawiera tożsamości użytkownika, poświadczeń i innych informacji o organizacji. Wiele subskrypcji platformy Azure może być skojarzony z pojedynczej dzierżawy usługi Azure Active Directory.

Aby dowiedzieć się więcej, zobacz [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Subskrypcja platformy Azure umożliwia tworzenie wystąpienia usług platformy Azure. Azure IoT Central automatycznie znajdzie wszystkie subskrypcje platformy Azure, odbiorcy, do których masz dostęp, a następnie wyświetli je na liście rozwijanej na **Utwórz aplikację** strony. Wybierz subskrypcję platformy Azure, aby utworzyć nową aplikację Central IoT Azure.

Jeśli nie masz subskrypcji platformy Azure, możesz go utworzyć w programie Microsoft Partner Center. Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji**. Nowa subskrypcja zostanie wyświetlona na liście rozwijanej **Subskrypcja platformy Azure**.

Aby dowiedzieć się więcej, zobacz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Obszar

Wybierz region, w którym chcesz utworzyć aplikację usługi Azure IoT centralnej. Zazwyczaj należy wybrać region znajdujący się najbliżej fizycznie urządzenia, aby uzyskać optymalną wydajność.

Aby dowiedzieć się więcej, zobacz [regionów świadczenia usługi Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Możesz zobaczyć regionów, w których usługa Azure IoT Central jest dostępna na [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/) strony.

> [!Note]
> Po wybraniu regionu nie można później przenieść aplikacji do innego regionu.

## <a name="application-template"></a>Szablon aplikacji

Można wybrać jedną z szablonów aplikacji dostępnych w nowej aplikacji usługi Azure IoT Central. Szablon aplikacji może zawierać wstępnie zdefiniowane elementy, takie jak pulpity nawigacyjne i szablony urządzeń, które pomogą Ci rozpocząć pracę.

| Szablon aplikacji | Opis |
| -------------------- | ----------- |
| Aplikacja niestandardowa   | Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń. |
| Przykład Contoso       | Tworzy aplikację, która zawiera szablon urządzenia dla prostych podłączonego urządzenia. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
| Przykład dotyczący zestawów deweloperskich       | Tworzy aplikację z gotowymi szablonami urządzeń, które umożliwiają łączenie z urządzeniem MXChip lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś deweloperem urządzenia, eksperymentowanie z kodem na jednym z tych urządzeń. |

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak utworzyć aplikację usługi Azure IoT Central jako dostawcy usług Kryptograficznych, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)