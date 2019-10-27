---
title: Tworzenie aplikacji IoT Central platformy Azure i zarządzanie nimi z poziomu portalu programu CSP | Microsoft Docs
description: Jako dostawca usług kryptograficznych, jak utworzyć aplikację IoT Central platformy Azure w imieniu klienta.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 2acac429dc8f0d9981e4c952702edd24b598881b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950901"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Tworzenie aplikacji IoT Central platformy Azure i zarządzanie nią z poziomu portalu CSP

Program Microsoft Cloud Provider (CSP) to program odsprzedawców firmy Microsoft. Jego celem jest zapewnienie partnerom kanałów zaniechania odsprzedaży wszystkich komercyjnych usług online firmy Microsoft. Dowiedz się więcej o [programie Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Jako dostawca usług kryptograficznych możesz tworzyć Microsoft Azure aplikacje IoT Central i zarządzać nimi w imieniu klientów za pomocą [Centrum partnerskiego firmy Microsoft](https://partnercenter.microsoft.com/partner/home). W przypadku tworzenia aplikacji IoT Central platformy Azure w imieniu klientów przez dostawców CSP, podobnie jak w przypadku innych usług platformy Azure zarządzanych przez CSP, dostawcy CSP zarządzają rozliczeniami dla klientów. Opłata za usługę Azure IoT Central zostanie wyświetlona w łącznym rachunku w centrum partnerskim firmy Microsoft.

Aby rozpocząć, zaloguj się do swojego konta w portalu Microsoft Partner Portal i wybierz klienta, dla którego chcesz utworzyć aplikację IoT Central platformy Azure. Przejdź do usługi Service Management dla klienta z lewej strony nawigacyjnej.

![Centrum partnerskie firmy Microsoft, widok klienta](media/howto-create-application-csp/image1.png)

IoT Central platformy Azure jest wyświetlana jako usługa dostępna do administrowania. Wybierz link IoT Central platformy Azure na stronie, aby utworzyć nowe aplikacje lub zarządzać istniejącymi aplikacjami dla tego klienta.

![Usługa Azure IoT Central dostępna do zarządzania](media/howto-create-application-csp/image2.png)

Na stronie Menedżer aplikacji IoT Central platformy Azure. Usługa Azure IoT Central utrzymuje kontekst, który pochodzi z Centrum partnerskiego firmy Microsoft i który został dostarczony do zarządzania tym konkretnym klientem. Ten komunikat jest wyświetlany w nagłówku strony Menedżera aplikacji. W tym miejscu możesz przejść do istniejącej aplikacji utworzonej wcześniej dla tego klienta, aby zarządzać lub utworzyć nową aplikację dla klienta.

![Tworzenie Menedżera dla dostawców CSP](media/howto-create-application-csp/image3.png)

Aby utworzyć aplikację IoT Central platformy Azure, wybierz kafelek **Nowa aplikacja** . Spowoduje to załadowanie strony tworzenia aplikacji. Musisz ukończyć wszystkie pola na tej stronie, a następnie wybierz pozycję **Utwórz**. Znajdziesz więcej informacji na temat każdego z poniższych pól.

![Tworzenie strony aplikacji dla dostawców CSP](media/howto-create-application-csp/image4.png)

![Tworzenie strony aplikacji dla dostawców CSP](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>Plan płatności

Jako dostawcę CSP można tworzyć tylko aplikacje z opcją płatność zgodnie z rzeczywistym użyciem. Aby zaprezentować IoT Central platformy Azure klientom, możesz utworzyć aplikację próbną osobno. Więcej informacji na temat wersji próbnej i aplikacji z opcją płatność zgodnie z rzeczywistym użyciem można znaleźć na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nazwa aplikacji

Nazwa aplikacji jest wyświetlana na stronie **Menedżera aplikacji** i w poszczególnych aplikacjach IoT Central platformy Azure. Możesz wybrać dowolną nazwę aplikacji IoT Central platformy Azure. Wybierz nazwę, która ma sens dla Ciebie i innych użytkowników w organizacji.

## <a name="application-url"></a>Adres URL aplikacji

Adres URL aplikacji to link do aplikacji. Możesz zapisać zakładkę w swojej przeglądarce lub udostępnić ją innym osobom.

Po wprowadzeniu nazwy aplikacji zostanie automatycznie wygenerowany adres URL aplikacji. Jeśli wolisz, możesz wybrać inny adres URL dla aplikacji. Każdy adres URL usługi Azure IoT Central musi być unikatowy w ramach usługi Azure IoT Central. Jeśli wybrany adres URL został już zajęty, zobaczysz komunikat o błędzie.

## <a name="directory"></a>Katalog

Ponieważ usługa Azure IoT Central ma kontekst do zarządzania klientem wybranym w portalu Microsoft Partner, w polu Katalog zostanie wyświetlona tylko dzierżawa Azure Active Directory dla tego klienta. 

Dzierżawa Azure Active Directorya zawiera tożsamości użytkowników, poświadczenia i inne informacje o organizacji. Z jedną dzierżawą Azure Active Directory można skojarzyć wiele subskrypcji platformy Azure.

Aby dowiedzieć się więcej, zobacz [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Subskrypcja platformy Azure umożliwia tworzenie wystąpień usług platformy Azure. Usługa Azure IoT Central automatycznie odnajdzie wszystkie subskrypcje platformy Azure, do których masz dostęp, i wyświetla je na liście rozwijanej na stronie **Tworzenie aplikacji** . Wybierz subskrypcję platformy Azure, aby utworzyć nową aplikację usługi Azure IoT Central.

Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć w centrum partnerskim firmy Microsoft. Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji**. Nowa subskrypcja zostanie wyświetlona na liście rozwijanej **Subskrypcja platformy Azure**.

Aby dowiedzieć się więcej, zobacz [subskrypcje platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Wybierz [region lub lokalizację geograficzną, w](https://azure.microsoft.com/global-infrastructure/geographies/) której chcesz utworzyć aplikację IoT Central platformy Azure. Zazwyczaj należy wybrać region znajdujący się najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność.

> [!NOTE]
> Szablon **aplikacji w wersji zapoznawczej** jest obecnie dostępny tylko w regionach **Europy Północnej** i **środkowe stany USA** .

Aby dowiedzieć się więcej, zobacz [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/) i [usługa Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/).

Możesz zobaczyć regiony, w których usługa Azure IoT Central jest dostępna na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) .

> [!Note]
> Po wybraniu regionu nie można później przenieść aplikacji do innego regionu.

## <a name="application-template"></a>Szablon aplikacji

Możesz wybrać jeden z dostępnych szablonów aplikacji dla nowej aplikacji IoT Central platformy Azure. Szablon aplikacji może zawierać wstępnie zdefiniowane elementy, takie jak pulpity nawigacyjne i szablony urządzeń, które pomogą Ci rozpocząć pracę.

| Szablon aplikacji | Opis |
| -------------------- | ----------- |
| Aplikacja niestandardowa   | Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń. |
| Przykład dotyczący firmy Contoso       | Tworzy aplikację, która zawiera szablon urządzenia dla prostego podłączonego urządzenia. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
| Przykład dotyczący zestawów deweloperskich       | Tworzy aplikację z gotowymi szablonami urządzeń, które umożliwiają łączenie z urządzeniem MXChip lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś deweloperem urządzenia z kodem na jednym z tych urządzeń. |

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy wiesz już, jak utworzyć aplikację IoT Central platformy Azure jako dostawcę CSP, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administruj swoją aplikacją](howto-administer.md)