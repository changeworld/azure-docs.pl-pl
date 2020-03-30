---
title: Tworzenie aplikacji usługi Azure IoT Central i zarządzanie nimi z portalu dostawcy usług kryptograficznych | Dokumenty firmy Microsoft
description: Jako CSP, jak utworzyć aplikację Azure IoT Central w imieniu klienta.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 5d876d7db13ce73603f212ec11544cb2f680bf46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158184"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Tworzenie aplikacji Azure IoT Central i zarządzanie nią z portalu CSP

Program Microsoft Cloud Solution Provider (CSP) jest programem odsprzedawcy firmy Microsoft. Jego zamiarem jest zapewnienie naszym partnerom handlowym kompleksowego programu odsprzedaży wszystkich komercyjnych usług online firmy Microsoft. Dowiedz się więcej o [programie Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Jako CSP możesz tworzyć aplikacje IoT Central platformy Microsoft Azure i zarządzać nimi w imieniu klientów za pośrednictwem [Centrum partnerów firmy Microsoft.](https://partnercenter.microsoft.com/partner/home) Gdy aplikacje Usługi Azure IoT Central są tworzone w imieniu klientów przez dostawców usług kryptograficznych, podobnie jak w przypadku innych usług platformy Azure zarządzanych przez dostawców usług CSP, dostawcy usług kryptograficznych zarządzają rozliczeniami dla klientów. Opłata za usługę Azure IoT Central pojawi się na całkowitym rachunku w Centrum partnerów firmy Microsoft.

Aby rozpocząć, zaloguj się do swojego konta w portalu partnerów firmy Microsoft i wybierz klienta, dla którego chcesz utworzyć aplikację Azure IoT Central. Przejdź do usługi Zarządzanie usługami dla klienta z lewej strony nawigacji.

![Centrum partnerów firmy Microsoft, widok klienta](media/howto-create-and-manage-applications-csp/image1.png)

Usługa Azure IoT Central jest wyświetlana jako usługa dostępna do administrowania. Wybierz łącze Azure IoT Central na stronie, aby utworzyć nowe aplikacje lub zarządzać istniejącymi aplikacjami dla tego klienta.

![Usługa Azure IoT Central dostępna do zarządzania](media/howto-create-and-manage-applications-csp/image2.png)

Wylądujesz na stronie centralnego menedżera aplikacji usługi Azure IoT. Usługa Azure IoT Central przechowuje kontekst, który pochodzi z Centrum partnerów firmy Microsoft i który przyszedł do zarządzania tym konkretnym klientem. Zostanie to potwierdzone w nagłówku strony Menedżera aplikacji. W tym miejscu można przejść do istniejącej aplikacji utworzonej wcześniej dla tego klienta, aby zarządzać lub utworzyć nową aplikację dla klienta.

![Tworzenie menedżera dla dostawców usług internetowych](media/howto-create-and-manage-applications-csp/image3.png)

Aby utworzyć aplikację Azure IoT Central, wybierz pozycję **Buduj** w menu po lewej stronie. Wybierz jeden z szablonów branżowych lub wybierz **starszą aplikację,** aby utworzyć aplikację od podstaw. Spowoduje to załadowanie strony Tworzenie aplikacji. Należy wypełnić wszystkie pola na tej stronie, a następnie wybrać pozycję **Utwórz**. Więcej informacji na temat każdego z poniższych pól.

![Tworzenie strony aplikacji dla dostawców usług internetowych](media/howto-create-and-manage-applications-csp/image4.png)

![Tworzenie strony aplikacji dla dostawców usług internetowych](media/howto-create-and-manage-applications-csp/image4-1.png)

![Tworzenie strony aplikacji dla informacji rozliczeniowych dostawców usług csps](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Plan cenowy

Można tworzyć tylko aplikacje, które używają standardowego planu cenowego jako CSP. Aby zaprezentować klientowi usługę Azure IoT Central, można utworzyć aplikację, która używa bezpłatnego planu cenowego oddzielnie. Dowiedz się więcej o bezpłatnych i standardowych planach cenowych na stronie cennik Azure [IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Można tworzyć tylko aplikacje, które używają standardowego planu cenowego jako CSP. Aby zaprezentować klientowi usługę Azure IoT Central, można utworzyć aplikację, która używa bezpłatnego planu cenowego oddzielnie. Dowiedz się więcej o bezpłatnych i standardowych planach cenowych na stronie cennik Azure [IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nazwa aplikacji

Nazwa aplikacji jest wyświetlana na stronie **Menedżera aplikacji** i w każdej aplikacji Usługi Azure IoT Central. Możesz wybrać dowolną nazwę aplikacji Usługi Azure IoT Central. Wybierz nazwę, która ma sens dla Ciebie i innych osób w organizacji.

## <a name="application-url"></a>Adres URL aplikacji

Adres URL aplikacji jest łączem do aplikacji. Zakładkę można zapisać w przeglądarce lub udostępnić innym osobom.

Po wprowadzeniu nazwy aplikacji adres URL aplikacji jest automatyczniegenerowany. Jeśli wolisz, możesz wybrać inny adres URL dla aplikacji. Każdy centralny adres URL usługi Azure IoT musi być unikatowy w centrum usługi Azure IoT. Jeśli wybrany adres URL został już pobrany, zostanie wyświetlony komunikat o błędzie.

## <a name="directory"></a>Katalog

Ponieważ usługa Azure IoT Central ma kontekst, w który przyszedł do zarządzania klientem wybranym w portalu partnerów firmy Microsoft, w polu Katalog jest widoczna tylko dzierżawa usługi Azure Active Directory dla tego klienta. 

Dzierżawa usługi Azure Active Directory zawiera tożsamości użytkowników, poświadczenia i inne informacje organizacyjne. Wiele subskrypcji platformy Azure można skojarzyć z jedną dzierżawą usługi Azure Active Directory.

Aby dowiedzieć się więcej, zobacz [Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Subskrypcja platformy Azure umożliwia tworzenie wystąpień usług platformy Azure. Usługa Azure IoT Central automatycznie wyszukuje wszystkie subskrypcje platformy Azure klienta, do którego masz dostęp, i wyświetla je w rozwijanej na stronie **Tworzenie aplikacji.** Wybierz subskrypcję platformy Azure, aby utworzyć nową aplikację centralną Usługi Azure IoT.

Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć w Centrum partnerów firmy Microsoft. Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji**. Nowa subskrypcja zostanie wyświetlona na liście rozwijanej **Subskrypcja platformy Azure**.

Aby dowiedzieć się więcej, zobacz [Subskrypcje platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Lokalizacja

**Lokalizacja** to [lokalizacja,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej urządzeń, aby uzyskać optymalną wydajność. Obecnie można utworzyć aplikację IoT Central w regionach **Australia,** **Azja i Pacyfik,** **Europa,** **Stany Zjednoczone,** **Wielka Brytania**i **Japonia.** Po wybraniu lokalizacji nie można później przenieść aplikacji do innej lokalizacji.

## <a name="application-template"></a>Szablon aplikacji

Wybierz szablon aplikacji, którego chcesz użyć dla aplikacji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz instrukcje tworzenia aplikacji Azure IoT Central jako CSP, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)
