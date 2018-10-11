---
title: Samouczek — tworzenie rekordu aliasu usługi Azure DNS do odwoływania do rekordu zasobów w strefie DNS.
description: W tym samouczku przedstawiono, jak skonfigurować rekord aliasu usługi Azure DNS do odwoływania do rekordu zasobów w strefie DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990845"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Samouczek: tworzenie rekordu aliasu do odwoływania do rekordu zasobów w strefie DNS

Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów DNS CNAME może być aliasem dla innego zestawu rekordów CNAME tego samego typu. Jest to przydatne, jeśli chcesz, by z punktu widzenia zachowania niektóre zestawy rekordów były aliasami, a niektóre nie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie rekordu aliasu dla rekordu zasobów w strefie DNS
> * Testowanie rekordu aliasu


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną, w tym możliwość ustawiania dla domeny rekordów serwera nazw.

Aby uzyskać instrukcje dotyczące hostowania własnej domeny w usłudze Azure DNS, zobacz temat [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Tworzenie rekordu aliasu

Utwórz rekord aliasu, który wskazuje na rekord zasobów w strefie DNS.

### <a name="create-the-target-resource-record"></a>Tworzenie docelowego rekordu zasobów
1. Kliknij strefę usługi Azure DNS, aby otworzyć strefę DNS.
2. Kliknij pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wpisz **serwer**.
4. Dla opcji **Typ** wybierz pozycję **A**.
5. W polu tekstowym **ADRES IP** wpisz **10.10.10.10**.
6. Kliknij przycisk **OK**.

### <a name="create-the-alias-record"></a>Tworzenie rekordu aliasu
1. Kliknij strefę usługi Azure DNS, aby otworzyć strefę DNS.
2. Kliknij pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wpisz **test**.
4. Dla opcji **Typ** wybierz pozycję **A**.
5. Kliknij **Tak** w polu wyboru **Zestaw rekordów aliasu** i wybierz opcję **Zestaw rekordów strefy**.
6. Dla opcji **Zestaw rekordów strefy** wybierz rekord **serwer**.
7. Kliknij przycisk **OK**.

## <a name="test-the-alias-record"></a>Testowanie rekordu aliasu

1. Uruchom ulubione narzędzie nslookup. Możesz na przykład przejść do [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Ustaw typ zapytania dla rekordów A i wyszukaj **test.\<twoja nazwa domeny\>**. Powinna zostać wyświetlona odpowiedź **10.10.10.10**.
3. W witrynie Azure Portal zmień rekord A **serwer** na **10.11.11.11**.
4. Poczekaj kilka minut, a następnie użyj narzędzia nslookup ponownie dla rekordu **test**.
5. Powinna zostać wyświetlona odpowiedź **10.11.11.11**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy rekordy zasobów **serwer** i **test** w Twojej strefie nie będą już potrzebne, możesz je usunąć.


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono rekord aliasu do odwoływania do rekordu zasobów w strefie DNS. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
