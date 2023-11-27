<<<<<<< HEAD
test


=======



# El comando Git pull en Git

Fusionar cambios del repositorio remoto al repositorio local es una tarea común en los flujos de trabajo colaborativos basados ​​en Git. El comando **git pull en Git** a menudo se usa para obtener y descargar contenido de un repositorio remoto y actualizar inmediatamente la working copy del repositorio local para que coincida con ese contenido.

El comando **git pull en Git**  es, en realidad, una combinación de otros dos comandos. 

En la primera fase de la operación, git pull realizará una búsqueda de git limitada solo a la rama a la que apunta la working copy. Una vez que se haya descargado el contenido, git pull ingresará a un flujo de reconciliación de commit que puede seguir dos métodos con efectos muy diferentes.

Entendámoslo mejor con un ejemplo:

Supongamos que hemos clonado un repository remoto cuando el último commit en su branch main era el indicado como “B”. Hemos hecho modificaciones y hemos guardado en local los commit “C”, “D” y “E”. Mientras tanto, alguien más ha creado y enviado al repository remoto otros commit, “F”, “G” y “H”

![Stato iniziale esempio merge](https://lh6.googleusercontent.com/20C6f6AbdTa9mzogrLIQxiioQF3GGX54-esT4zatd4wZIvF30Q6Gzi0waxauDsaZBuMbE7GCubFSyhvplC0r5avnbeDngscmq56La1K8ssEV2hezfytevLjFIXCLU0G2N8D4Mi4r-wWqAFdiHwCGscP1kyiRnw9G)

*Estado inicial ejemplo fusión*

En esta situación, teniendo en cuenta que el repositorio remoto es el que alberga la historia "oficial" del proyecto, tendremos que recuperar los nuevos commits remotos e integrarlos con los locales. No obstante, también tendremos que indicar la estrategia preferida con la que hacerlo.

Git, de hecho, ofrece dos formas distintas de pull/merger de ramas divergentes, con rebase o con fusión.

NOTA: en versiones recientes de Git, git pulls en ramas divergentes falla a menos que especifique la estrategia deseada. Puede hacer esto a través de argumentos de comando, pero también se puede especificar la estrategia predeterminada a través de git config.

## **Pull con rebase**

![Stato iniziale esempio merge](https://lh4.googleusercontent.com/cW2ZEYgjG55kqwF80i4xZEgjekAvMGX1P7d7sUkJK3-3DOlIZKTN-bI6K28PAf-LsDqjGWtMQRLyhpx-dBeWoT4RybCLI6mInM14mblZq8E_eY96Jm43MGvQxTD81LMl7d_vQjH-ib3bKXVSftz6HlMcQkDQ4t35)

*Estado inicial ejemplo merge*

El **pull con rebase** es el método que, en cierto sentido, respeta el contenido del repositorio remoto como "oficial" y considera los commits en el repositorio local como commits a aplicar sobre el historial actualizado del repositorio.

Ejecutando **git pull --rebase** sucede lo siguiente:

* se reservan los commit locales (C-D-E) añadidos respecto al último origin/main recuperado
* se ejecuta el fetch que recupera los nuevos commit desde el branch main del repository remoto origin
* los nuevos commit remotos vengono se aplican a la “copia” local origin/main y se actualiza el branch locale main y la working copy
* los commit locales C-D-E se “reaplican”, creando entonces nuevos commit C’-D’-E’, cada uno de los cuales contiene las mismas diferencias de los commit C-D-E

Recordamos, de hecho, que **un commit es un snapshot completo del estado de un repositorio**, por lo que habiendo cambiado el historial antes del commit C, el commit C' contendrá las mismas "diferencias" en comparación con la snapshot anterior, pero que sea un nuevo commit.

Es posible elegir esta estrategia como predeterminada mediante el comando **git config pull.rebase true**

## **Pull con merge**

![Stato iniziale esempio merge](https://lh6.googleusercontent.com/ovZFopxpMhPDZdYeHmIgeLTff7SQ4t3weJLX20h895us7z58Nc3pbro_4no0KoM66VA5ZDsCcSFax6ZUaXd_74fqYoOAvja1rnPmMPyPlvcauw-zVqRtxcszg1GnvSgM8gKXt4rOtWJpSywGOTSpkuam0ulqQCns)

*Estado inicial ejemplo merge*

El otro método es el de **pull with merge** que, en la situación descrita, da "precedencia" al repositorio local sobre el remoto, o al menos en la situación descrita podría crear una historia aparentemente inconsistente.

Al ejecutar **git pull --no-rebase** de hecho, ocurre lo siguiente:

* se ejecuta el fetch que recupera los nuevos commit del branch main del repository remoto origin
* se cogen los commit F-G-H recuperados del repository remoto y se crea un commit especial de “merge” (I) que aplica el completo diff al branch locale
* se pide al usuario guardar tal commit.

Al final del **pull con merge**, aunque estamos en una situación que desde el punto de vista de Git ya no es "divergente" (de hecho, será posible hacer git pull para enviar al repositorio remoto), el la historia del proyecto es menos comprensible y lineal.

De hecho, si vemos el historial del proyecto con **git log --graph  --oneline** después de hacer el push, obtendremos algo como esto:


    $ git log --graph  --oneline
    *   ece860f (HEAD -> main, origin/main, origin/HEAD) I - Merge branch 'main' of https://server.com/repository.git
    |\
    | * 09817f7 H - remote commit three
    | * 1e0e1d2 G - remote commit two
    | * b7d7502 F - remote commit one
    * | 4d49b1c E - local commit 3
    * | 6453336 D - local commit 2
    * | 24540dd C - local commit 1
    |/
    * 9cc4eee B - another commit
    * f46646d A - initial commit
Ten en cuenta que, desde un punto de vista estrictamente cronológico del repositorio remoto, la historia se ha invertido, ya que en la historia los commit F-G-H estaban, en la práctica, ya presentes antes de que se enviaran los commit C-D-E, que, sin embargo, ahora siguen directamente los commit A-B.

Aunque, por tanto, el "merge" es una actividad a veces necesaria en la gestión de la sincronización de trabajos en Git, en caso de colaborar en una rama es recomendable optar por el modo rebase.

Es posible elegir esta estrategia como default con el comando **git config pull.rebase false**
>>>>>>> f5c3359ae07b882c5f820cbda5fba2047f631c28
