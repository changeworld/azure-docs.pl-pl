---
title: Samouczek — tworzenie rekordu aliasu usługi Azure DNS do odwoływania do rekordu zasobów w strefie DNS.
description: W tym samouczku przedstawiono, jak skonfigurować rekord aliasu usługi Azure DNS do odwoływania do rekordu zasobów w strefie DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 3b4ee688d6a5606ab6008b459fcf6331c24afaae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61429794"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Samouczek: Tworzenie rekordu aliasu do odwoływania się do rekordu zasobu strefy

Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów DNS CNAME może być aliasem dla innego zestawu rekordów CNAME tego samego typu. Ta funkcja jest przydatna, jeśli chcesz, by z punktu widzenia zachowania niektóre zestawy rekordów były aliasami, a niektóre nie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie rekordu aliasu dla rekordu zasobów w strefie DNS.
> * Testowanie rekordu aliasu.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje, Hostuj swoją domenę, w usłudze Azure DNS, zobacz [samouczka: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Tworzenie rekordu aliasu

Utwórz rekord aliasu, który wskazuje na rekord zasobów w strefie DNS.

### <a name="create-the-target-resource-record"></a>Tworzenie docelowego rekordu zasobów
1. Wybierz strefę usługi Azure DNS, aby ją otworzyć.
2. Wybierz pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wprowadź **serwer**.
4. Dla opcji **Typ** wybierz pozycję **A**.
5. W polu tekstowym **ADRES IP** wprowadź **10.10.10.10**.
6. Kliknij przycisk **OK**.

### <a name="create-the-alias-record"></a>Tworzenie rekordu aliasu
1. Wybierz strefę usługi Azure DNS, aby ją otworzyć.
2. Wybierz pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wprowadź **test**.
4. Dla opcji **Typ** wybierz pozycję **A**.
5. Zaznacz pozycję **Tak** w polu wyboru **Zestaw rekordów aliasów**. Następnie wybierz opcję **Zestaw rekordów strefy**.
6. Dla opcji **Zestaw rekordów strefy** wybierz rekord **serwer**.
7. Kliknij przycisk **OK**.

## <a name="test-the-alias-record"></a>Testowanie rekordu aliasu

1. Uruchom ulubione narzędzie nslookup. Możesz na przykład przejść do [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Ustaw typ zapytania dla rekordów A i wyszukaj **test.\<Twoja nazwa domeny\>**. W odpowiedzi otrzymasz **10.10.10.10**.
3. W witrynie Azure Portal zmień rekord A **serwer** na **10.11.11.11**.
4. Poczekaj kilka minut, a następnie użyj ponownie narzędzia nslookup dla rekordu **test**. W odpowiedzi otrzymasz **10.11.11.11**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w ramach tego samouczka, usuń rekordy zasobów **serwer** i **test** w swojej strefie.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzono rekord aliasu do odwoływania do rekordu zasobów w strefie DNS. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
