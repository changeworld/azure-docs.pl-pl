---
title: Wprowadzenie do wbudowanej obsługi notesów Jupyter w usłudze Azure Cosmos DB (Wersja zapoznawcza)
description: Dowiedz się, jak można używać wbudowanej obsługi notesów Jupyter w usłudze Azure Cosmos DB do interaktywnego uruchamiania zapytań.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760287"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Wbudowana obsługa notesów Jupyter w usłudze Azure Cosmos DB (wersja zapoznawcza)

Notebook Jupyter to aplikacja internetowa typu open source, która umożliwia tworzenie i udostępnianie dokumentów zawierających kod na żywo, równania, wizualizacje i tekst narracyjny. Usługa Azure Cosmos DB obsługuje wbudowane notesy Jupyter dla wszystkich interfejsów API, takich jak Cassandra, MongoDB, SQL, Gremlin i Table. Wbudowana obsługa notesu dla wszystkich interfejsów API bazy danych usługi Azure Cosmos DB i modeli danych umożliwia interaktywne uruchamianie zapytań. Notesy Jupyter są uruchamiane na kontach usługi Azure Cosmos i umożliwiają deweloperom przeprowadzanie eksploracji danych, czyszczenia danych, przekształceń danych, symulacji numerycznych, modelowania statystycznego, wizualizacji danych i uczenia maszynowego.

![Wizualizacje notesów Jupyter w usłudze Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Notebooki Jupyter obsługują magiczne funkcje, które rozszerzają możliwości jądra, obsługując dodatkowe polecenia. Magia usługi Cosmos to polecenie rozszerzające możliwości jądra języka Python w notesie Jupyter, dzięki czemu można uruchamiać kwerendy interfejsu API SQL usługi Azure Cosmos oprócz platformy Apache Spark. Można łatwo łączyć zapytania interfejsu API języka Python i SQL, aby wyszukiwać i wizualizować dane przy użyciu rozbudowanych bibliotek wizualizacji zintegrowanych z poleceniami renderowania.
Portal Azure natywnie integruje środowisko notesu Jupyter z kontami usługi Azure Cosmos, jak pokazano na poniższej ilustracji:

![Obsługa notesów Jupyter w usłudze Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Zalety notebooków Jupyter

Notebooki Jupyter zostały pierwotnie opracowane dla aplikacji do nauki o danych napisanych w pythonie, R. Mogą one jednak być używane na różne sposoby dla różnych rodzajów projektów, takich jak:

* ***Wizualizacje danych:** Notesy Jupyter umożliwiają wizualizację danych w postaci udostępnionego notesu, który renderuje niektóre zestawy danych jako grafikę. Notes Jupyter umożliwia tworzenie wizualizacji, udostępnianie ich i umożliwianie interaktywnych zmian w udostępnionym kodzie i zestawie danych.

* **Udostępnianie kodu:** Usługi takie jak GitHub zapewniają sposoby udostępniania kodu, ale w dużej mierze nie są interaktywne. Za pomocą notesu Jupyter można wyświetlić kod, wykonać go i wyświetlić wyniki bezpośrednio w witrynie Azure portal.

* **Interakcje na żywo z kodem:** Kod notebooka Jupyter jest dynamiczny; może być edytowany i ponownie uruchamiany przyrostowo w czasie rzeczywistym. Notesy mogą również osadzać formanty użytkownika (np. suwaki lub pola wprowadzania tekstu), które mogą być używane jako źródła wejściowe kodu, dema lub proof of concepts (POC).

* **Dokumentacja próbek kodu i wyników eksploracji danych:** Jeśli masz fragment kodu i chcesz wyjaśnić wiersz po wierszu, jak to działa w usłudze Azure Cosmos DB, z danych wyjściowych w czasie rzeczywistym po drodze, można osadzić go w notesie Jupyter. Kod pozostanie w pełni funkcjonalny. Można dodać interaktywność wraz z dokumentacją w tym samym czasie.

* **Magiczne polecenia Kosmosu:** W notesach Jupyter można użyć niestandardowych poleceń magicznych dla usługi Azure Cosmos DB, aby ułatwić przetwarzanie interaktywne. Na przykład %%sql magic, który pozwala na kwerendę kontenera usługi Cosmos przy użyciu interfejsu API SQL bezpośrednio w notesie.

* **Wszystko w jednym miejscu:** Notesy Jupyter łączą kod, tekst sformatowany, obrazy, filmy, animacje, równania matematyczne, wykresy, mapy, interaktywne figury, widżety i graficzne interfejsy użytkownika w jednym dokumencie.

## <a name="components-of-a-jupyter-notebook"></a>Składniki notebooka Jupyter

Notebooki Jupyter mogą zawierać kilka typów komponentów, z których każdy jest podzielony na oddzielne bloki:

* **Tekst i kod HTML:** W dowolnym momencie do dokumentu można wstawić zwykły tekst lub tekst z adnotacjami w składni znaczników w celu wygenerowania kodu HTML. Styl CSS można również uwzględnić w linii lub dodać do szablonu używanego do generowania notesu.

* **Kod i wyjście:** Notebooki Jupyter obsługują kod języka Python. Wyniki wykonanego kodu pojawiają się natychmiast po blokach kodu, a bloki kodu mogą być wykonywane wiele razy w dowolnej kolejności.

* **Wizualizacje:** Grafika i wykresy mogą być generowane z kodu, przy użyciu modułów, takich jak Matplotlib, Plotly lub Bokeh. Podobnie jak dane wyjściowe, te wizualizacje są wyświetlane w linii wbudowanej obok kodu, który je generuje.

* **Multimedia:** Ponieważ notebook Jupyter jest zbudowany na technologii internetowej, może wyświetlać wszystkie rodzaje multimediów obsługiwanych na stronie internetowej. Można je dołączyć do notesu jako elementy HTML lub można je `IPython.display` wygenerować programowo za pomocą modułu.

* **Dane:** Dane z kontenerów usługi Azure Cosmos i wyniki zapytań można zaimportować programowo do notesu Jupyter. Na przykład przez dołączenie kodu w notesie do kwerendy danych przy użyciu dowolnego interfejsu API usługi Cosmos DB lub natywnie wbudowany Apache Spark.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z wbudowanymi notesami Jupyter w usłudze Azure Cosmos DB, zobacz następujące artykuły:

* [Włączanie notesów na koncie usługi Azure Cosmos](enable-notebooks.md)
* [Korzystanie z funkcji i poleceń notesu](use-notebook-features-and-commands.md)



