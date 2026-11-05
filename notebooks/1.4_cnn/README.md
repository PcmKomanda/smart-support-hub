# 1.4 dalis — CNN (konvoliucinis neuroninis tinklas)

Užduoties 1.4 punktui skirta užrašinė `1_4_cnn_query_images.ipynb`: originalus
„Colab CNN“ (MNIST stiliaus vaizdų klasifikavimas) suprastas ir perdarytas
su savais duomenimis — kliento užklausa **nupiešiama kaip pustonių (angl. _grayscale_)
64×256 vaizdas** (`PIL`), o CNN išmoksta iš vaizdo atpažinti ketinimo klasę.

Procesų seka (angl. _pipeline_): `Užklausa (X) → PIL teksto piešimas → pustonių vaizdas → Conv2D × 2 + MaxPool + Dense → ketinimas (y)`.

Duomenys — taip pat 252 (X, y) poros iš `data/customer_support_dataset.csv`;
testavimas — ta pati kanoninė 6 užklausų aibė, kaip ir 1.3 bei 1.2.2 dalyse.

GPU nebūtinas, bet pagreitins mokymąsi (_Runtime → Change runtime type → T4 GPU_).
