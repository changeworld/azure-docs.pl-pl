---
title: Przygotowywanie do zmiany adresu IP protokołu SSL
description: Jeśli adres IP protokołu SSL zostanie zmieniony, Dowiedz się, co należy zrobić, aby aplikacja nadal działała po zmianie.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 34f35eb67cada6066e35227fcd6a0eaf425ac007
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672401"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Jak przygotować się do zmiany adresu IP protokołu SSL

Jeśli otrzymasz powiadomienie o zmianie adresu IP protokołu SSL aplikacji Azure App Service, postępuj zgodnie z instrukcjami w tym artykule, aby zwolnić istniejący adres IP protokołu SSL i przypisać nowy.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Zwolnij adresy IP protokołu SSL i przypisz nowe

1.  Otwórz [portal Azure](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **App Services**.

3.  Wybierz z listy aplikację App Service.

4.  W obszarze nagłówka **Ustawienia** kliknij pozycję **Ustawienia protokołu SSL** w lewym obszarze nawigacji.

1. W sekcji powiązania SSL wybierz rekord nazwy hosta. W otwartym edytorze wybierz pozycję **SNI SSL** z menu rozwijanego **Typ protokołu SSL** , a następnie kliknij pozycję **Dodaj powiązanie**. Gdy zostanie wyświetlony komunikat o powodzeniu operacji, istniejący adres IP został wystawiony.

6.  W sekcji **powiązania SSL** ponownie wybierz ten sam rekord nazwy hosta z certyfikatem. W edytorze, który zostanie otwarty, w menu rozwijanym **Typ protokołu SSL** wybierz pozycję **IP oparty na** protokole SSL, a następnie kliknij przycisk **Dodaj powiązanie**. Gdy zostanie wyświetlony komunikat o powodzeniu operacji, uzyskano nowy adres IP.

7.  Jeśli rekord A (rekord DNS wskazujący bezpośrednio na adres IP) jest skonfigurowany w portalu rejestracji domeny (dostawca DNS innej firmy lub Azure DNS), Zastąp istniejący adres IP nowo wygenerowanym. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Znajdowanie nowego adresu IP protokołu SSL w witrynie Azure Portal

1.  Zaczekaj kilka minut, a następnie otwórz [Azure Portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **App Services**.

3.  Wybierz z listy aplikację App Service.

4.  W obszarze nagłówka **Ustawienia** kliknij pozycję **Właściwości** na lewym pasku nawigacyjnym i Znajdź sekcję zatytułowaną **wirtualny adres IP**.

5. Skopiuj adres IP i ponownie skonfiguruj swój rekord domeny lub mechanizm IP.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak przygotować się do zmiany adresu IP, która została zainicjowana przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w Azure App Service, zobacz [adresy IP protokołu SSL i protokołu SSL w Azure App Service](overview-inbound-outbound-ips.md).
