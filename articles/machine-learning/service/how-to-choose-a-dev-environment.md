---
title: Środowiska projektowania dla usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Omówienie środowisk deweloperskich, które za pomocą usługi Azure Machine Learning. Python 3 jest jedynym wymaganiem dla swojego środowiska programowania, ale zaleca się również za pomocą środowisk Conda. Opracowywania narzędzi firma Microsoft zaleca notesów programu Jupyter, notesy platformy Azure i edytorów IDE/kodów.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 260e209bcf00396ee545851684038578c4fd148a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971046"
---
# <a name="development-environment-for-azure-machine-learning"></a>Środowisko projektowe dla usługi Azure Machine Learning 

Informacje na temat środowisk programowania, korzystających z usługi Azure Machine Learning. 

Usługa Azure Machine Learning jest niezależne od platformy i nie wymaga to środowisko projektowe określone. Wymaga ona __Python 3__, a firma Microsoft zaleca używanie __Conda__ tworzyć środowiska izolowanego. __Jeśli masz już środowisku deweloperskim, który spełnia te wymagania__, możesz pominąć ten dokument i przejdź do [skonfigurować środowisko deweloperskie](how-to-configure-environment.md) dokumentu.

Pozostała część tego dokumentu w tym artykule omówiono środowiska programowania, w których firma Microsoft zaleca:

* __Notesy programu Jupyter__
* __Notesy platformy Azure__
* __Zintegrowanych środowisk projektowych (IDE) oraz edytory kodu__
* __Maszyna wirtualna do nauki o danych__

Porównanie tych środowisk jest trudne, ponieważ można rozszerzyć notesów i środowisk IDE. Na przykład niektóre środowiska IDE może służyć jako klientów do notesów programu Jupyter. Ogólnie rzecz biorąc __notesów__ są przeznaczone dla __interakcyjne eksperymentowanie__ i __wizualizacji__. __Edytorów IDE i kodu__ oferuje narzędzia do __poprawią jakość kodu__ i __integracji z zewnętrznymi systemami__ takich jak kontrola wersji.

> [!TIP]
> Przy użyciu jednego środowiska nie można zablokować dostęp przy użyciu drugiej. Notesy i środowisk IDE są tylko narzędzi i mogą być mieszane, zgodnie z potrzebami. Na przykład użytkownik może mogą zacząć eksperymentować w notesie, a następnie wyeksportować do skryptu języka python, edytowanie i debugowanie w środowisku IDE.
>
> Jest to, dlaczego maszyny wirtualnej do nauki o danych udostępnia kilka popularnych środowisk IDE języka Python i notesy Jupyter.

## <a name="jupyter-notebooks"></a>Notesy programu Jupyter

Program Jupyter Notebooks jest częścią [projektu Jupyter](https://jupyter.org/). Są one ukierunkowana na zapewnienie interaktywne środowisko kodowania, w której utworzono dokumenty, które mieszać kodu na żywo z tekstu opisowego i grafiki. Notesy Jupyter notebook można zainstalować na wielu różnych platformach.

Gdy instalację notesu programu Jupyter możliwe do zainstalowania i skonfigurowania środowiska, zgodnie z potrzebami. Jednak odpowiedzialność za utrzymanie systemu.

## <a name="azure-notebooks"></a>Notesy platformy Azure

[Notesy platformy Azure](https://notebooks.azure.com) (wersja zapoznawcza) to środowisko notesów w chmurze platformy Azure. Opiera się na Jupyter i zapewnia środowisko, które są wstępnie załadowane z popularnymi bibliotekami. Zestaw SDK usługi Azure Machine Learning jest już zainstalowany w notesach platformy Azure, aby można było zacząć eksperymentować z prawie nie instalacji.

Notesy platformy Azure upraszcza proces udostępniania notesów programu. Możesz udostępnić adres URL do notesów programu, upublicznić biblioteki lub Udostępnij w mediach społecznościowych z interfejsu notesy platformy Azure.

Wadą notesów usługi Azure jest nie mieć pełną kontrolę nad środowiskiem i może nie móc instalowanie oprogramowania niestandardowego, które są potrzebne. Jest również środowisku współdzielonym, dlatego notesy może działać wolniej niż w dedykowanym instalacji notesu programu Jupyter.

## <a name="ides-and-code-editors"></a>Edytorów IDE i kodu

Istnieje wiele edytorów IDE i kodu, współpracujących z usługą Azure Machine Learning. Wymagania dotyczące tylko oprogramowania jest Azure Machine Learning zestaw SDK, który można zainstalować za pomocą `pip` narzędzia.

Firma Microsoft zaleca [programu Visual Studio Code](https://code.visualstudio.com/), ponieważ oferuje narzędzia do pracy z języka Python i usługi Azure Machine Learning. Jest ona dostępna dla platform Linux, macOS i Windows.

## <a name="data-science-virtual-machine"></a>Maszyna wirtualna do analizy danych

Maszyna wirtualna do nauki o danych (DSVM) składa się z poprzednich środowisk. Jest Maszynę wirtualną na platformie Azure, który ma notesów programu Jupyter i programu Visual Studio Code, Machine Learning zestawu SDK usługi Azure wstępnie zainstalowane. Tworzenie maszyny Wirtualnej jest bardziej skomplikowane niż notesy platformy Azure, ale mniej skomplikowany niż konfigurowaniem maszyny od podstaw. Ponieważ wymagane oprogramowanie jest wstępnie zainstalowane w obrazie maszyny Wirtualnej, możesz przystąpić do eksperymentowania w usłudze Azure Machine Learning szybko po utworzeniu maszyny Wirtualnej.

Maszyny DSVM służy do wybierania zasoby obliczeniowe, których potrzebujesz, takie jak procesor CPU, procesora GPU i pamięci. Jest on również wstępnie instalowany przy użyciu innych edytorów, takich jak platformy PyCharm, a także popularne usługi machine learning oprogramowania, takich jak TensorFlow, Keras i PyTorch. Jeśli nie zainstalowano oprogramowania, które są potrzebne, możesz zainstalować je samodzielnie.

Aby uzyskać więcej informacji na temat wstępnie zainstalowane, zobacz [co to jest maszyna wirtualna do nauki o danych](../data-science-virtual-machine/overview.md) dokumentu.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, skoro wiesz już o środowiskach programistycznych, [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md).

