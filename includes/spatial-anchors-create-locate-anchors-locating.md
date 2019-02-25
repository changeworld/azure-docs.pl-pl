## <a name="locating-a-cloud-spatial-anchor"></a>Lokalizowanie kotwica przestrzenne chmury

Aby zlokalizować przestrzenne kotwic chmury, musisz znać ich identyfikatorów. Identyfikatory zakotwiczeń mogą być przechowywane w usłudze zaplecza aplikacji s i dostępne dla wszystkich urządzeń, które poprawnie uwierzytelnił się do niego. Przykładem tego zobacz [samouczka: Udostępniaj przestrzenne zakotwiczenia między urządzeniami](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Utwórz wystąpienie obiektu AnchorLocateCriteria, Ustaw identyfikatory, czego szukasz i wywoływać metodę CreateWatcher sesji, zapewniając Twojej AnchorLocateCriteria.
