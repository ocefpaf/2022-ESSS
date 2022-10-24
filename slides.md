---
title: Meu produto está pronto, e agora?
author: Filipe Fernandes
date: Oct 27, 2022
history: true

subtitle: Empacotando e distribuindo Software para Computação Científica
---


# O que é cultura?

É uma rede de compartilhamento de símbolos,
significados e valores de um grupo ou sociedade.
São formados artificialmente pelos humanos,
de uma maneira **não natural** ao longo de seu **desenvolvimento**.

. . . 

Logo, por definição, precisamos saber a **origem** e **estória** de cada um para entender sua cultura.

# Aviso

Bom, isso foi uma forma longa de dizer que tudo nessa apresentação terá opiniões,
e não necessariamente é a solução para todos os problemas.

# Minha origem e estória

>- Oceanógrafo, mestre em Oc. Física, _PhD dropout_.
>- Comecei com Fortran/Matlab, e como todo pesquisador, meu desafio número 2 era compilar/instalar Software.
>- Inevitavelmente acabei me tornando o "empacotador" do lab.

. . .

Hoje sou _developer advocate_ da National Oceanic and Atmospheric Administration (NOAA) no time do Integrated Ocean Observing System (IOOS).


# Como cheguei aqui?

Em computação científica ou você morre resolvendo bugs\* em código legado ou vive o suficiente para se tornar empacotador.

\* que você mesmo criou.


# Mas o que é um "pacote científico (Python)?"

![](images/wonderful-01.jpg)

# Nenhuma solução abrange +80% dos problemas

Uma breve revisão de empacotamento em Python.

>- Python egg (2004-2012): "binário", zip do código fonte, multi-plataforma ("importable").
>- Python sdist (?-hoje): evolução do egg, ainda apenas código fonte\*.
>- Pacotes conda (2012-hoje): instaláveis pelo gerenciador de pacote agnóstico\* conda.
>- Python wheels (2012-hoje): PEP 427, distribuição binária com código compilado.


# conda vs wheels parte 1

|              | conda                            | wheels                    |
|--------------|----------------------------------|---------------------------|
| solver       | primeira versão                  | no pip ~2020              |
| shared libs  | são dependências                 | são "bundled" \*          |
| gerencia     | qualquer pacote até mesmo Python | apenas pacotes Python \*  |

# cmake

![](images/pypi-cmake.png)

# llvm

![](images/pypi-llvm.png)

# Então usar pip é um desperdício de espaço?

```shell
conda create --name CONDA python=3.10 rasterio fiona pyproj geopandas shapely rtree netcdf4 h5py h5netcdf
```

```
conda create --name PIP python=3.10 pip && conda activate PIP
pip install rasterio fiona pyproj geopandas shapely rtree netcdf4 h5py h5netcdf
```

. . .
```
du -skch *
938M	CONDA
393M	PIP
```


# Então o "bundle" não é tão ruim assim?

![](images/wheel-compat.png)


# Fazendo uma dieta

```
micromamba/envs/PIP via 🅒 CLI 
❯ lr | grep libgdal

26M Oct 21 14:41 libgdal-f07e835c.so.30.0.3
29M Oct 21 14:41 libgdal-fe50f3e8.so.31.0.2
```

```
micromamba/envs/PIP via 🅒 CONDA
❯ lr | grep libgdal

100K Oct 21 14:39 libgdal-3.5.2-h6e88209_4.json
libgdal.so -> libgdal.so.31.0.2
libgdal.so.31 -> libgdal.so.31.0.2
36M Oct 21 14:39 libgdal.so.31.0.2
```


# now for something completely different

![](images/wheel_size.jpg)



# conda vs wheels parte 2

|              | conda                            | wheels                                                                                                  |
|--------------|----------------------------------|---------------------------------------------------------------------------------------------------------|
| instala pkg? | &check;                          | `pip` ou `flit/flit-core` ou `poetry/poetry-core` ou `hatch/hatchling` ou `pdm` ou `pipenv` ou (insira aqui sua última busca do Google) |


# conda vs wheels parte 3

|              | conda                            | wheels                                                                                                      |
|--------------|----------------------------------|-------------------------------------------------------------------------------------------------------------|
| envs         | &check;                          | `venv` ou `pyenv` ou `virtualenv`+`virtualenvwrapper` ou `pipx` e/ou alguns dos acima                       |
| empacota?    | &check;                          | `build` ou `setuptools` ou `pep517` e/ou `packaging` e/ou `twine` e/ou `audiwheel` e/ou alguns dos acima    |


# conda vs wheels parte 4

|              | conda                            | wheels                                       |
|--------------|----------------------------------|----------------------------------------------|
| lockfile     | plugin externo                   | `piptools` ou `pipenv` e/ou alguns dos acima |


# Em resumo

![](images/One_Ring_Blender_Render.png)

# OK, sou apenas um usuário que quer instalar e gerenciar envs

![](images/python_environment.png)

# Escolhas... Conda como gerenciador de pacotes.

  *  ~80% dos recursos em um único comando
  * permite maior flexibilidade em "linkar" pacotes from shared libs
  * permite a criação da variantes
  * pacotes não são responsabilidade do desenvolvedor, mas da comunidade
  * 100% OSS
  * comunidade diversa e aberta
  * múltiplas empresas envolvidas


# Escolhas... Conda como gerenciador de ambientes

  * instala Python e outras linguagens
  * não precisa de acesso elevado na máquina
  * ambientes são encorajados e reduz a confusão de caminhos e variáveis
  * ambientes podem ser criados "from history", arquivo yaml ou _lockfiles_


# OK, mas como empacotar?

[https://mathspp.com/blog/how-to-create-a-python-package-in-2022](https://mathspp.com/blog/how-to-create-a-python-package-in-2022)

![](images/qr-more_resources.png)

# 5 estórias, 5 pesquisadores

![](images/forest.jpg)

# Nossos pesquisadores

>- Angela: meteorologista, especialista em "Convective Available Potential Energy", Fortran.
>- Marco: aprendeu Rust depois de se formar e anda com o livro "re-escreva tudo em Rust" debaixo do braço.
>- Anderson: HTML/JS/npm, faz módulos para Jupyter/JupyterLab nas horas vagas.
>- Paula: matemática, _expert_ em Julia, ajuda a Angela em um projeto de compressão de dados.
>- Tália: aluna de graduação, aprendendo Python, precisa ler os dados da Paula para seu TCC.

# Angela

>- Missão: Calcular CAPE Convective Available Potential Energy.
>- Código: Pronto, em Fortran, padrão testado e publicado.
>- Operação: Rodar o CAPE em resultados de modelo numéricos que carrega pelo `xarray`.
>- Dificuldade: Interoperabilidade entre Python-Fortran .

. . .

Solução: `numpy.distutils.fcompiler` + `conda`

# setup.py is dead, vida longa ao setup.py

```python
from numpy.distutils.core import setup, Extension
from numpy.distutils.fcompiler import get_default_fcompiler, CompilerNotFound

compiler = get_default_fcompiler()
f90flags = []
if compiler == 'gnu95':
    f90flags.append('-ffree-form')

extensions = ['stdheight_2D_model_lev', 'stdheight_2D_pressure_lev']

def _mk_sources(name):
    return [f'src/xcape/fortran/{name}.pyf', f'src/xcape/fortran/{name}.f90']

ext_modules = [Extension(name='fortran.' + name, sources=_mk_sources(name),
extra_f90_compile_args=f90flags, f2py_options=['--quiet'])
for name in extensions]
```

# Empacotando tudo

```yaml
requirements:
  build:
    - {{ compiler('fortran') }}  # [not win]
    - {{ compiler('m2w64_fortran') }}  # [win]
  host:
    - python
    - pip
    - numpy
  run:
    - python
    - xarray >=0.14.1
    - dask
    - {{ pin_compatible('numpy') }}
```


# oldest-supported-numpy

![](images/pypi-oldest-supported-numpy.png)


# Marco

>- Missão: Calcular o MinHash de assinaturas de DNA
>- Código: Reescreveu o Fortran original em Rust.
>- Operação: Distribuir um binário para centenas de geneticistas alérgicos a computador.
>- Dificuldade: Compilar e distribuir um código Rust+Python.

. . .

Solução: `cffi+milksnake` + `conda`

# cffi+milksnake para o salvamento

```python
from setuptools import setup, find_packages

def build_native(spec):
    build = spec.add_external_build(
        cmd=['cargo', 'build', '--release'],
        path='./rust'
    )
    spec.add_cffi_module(
        module_path='example._native',
        dylib=lambda: build.find_dylib(
          'example', in_path='target/release'),
        header_filename='include/example.h',
        rtld_flags=['NOW', 'NODELETE']
    )
```

# Setup

```python
setup(
    name='example',
    packages=find_packages(),
    include_package_data=True,
    setup_requires=['milksnake'],
    install_requires=['milksnake'],
    milksnake_tasks=[build_native],
)
```

# Empacotando tudo

```yaml
requirements:
  build:
    - python
    - cffi
    - {{ compiler('rust') }} >=1.48.0
```

# Anderson

>- Missão: Ajudar o pessoal do lab com a extensão "favorite folders" para o JupyterLab.
>- Código: Uma extensão para o JupyterLab super útil mas requer comandos extras (npm) para instalar após o `pip install`.
>- Operação: Facilitar a instalação dessa extensão.
>- Dificuldade: A extensão depende de outro gerenciador de pacotes, o npm, para finalizar a instalação.

# para o conda o npm é só outro pacote

```yaml
requirements:
  host:
    - nodejs >=12.0.0
    - python >=3.6
    - pip
    - jupyterlab >=3.0.0
    - jupyter-packaging >=0.7.9,<0.8.0
```

# Recapitulando

Usamos o conda para,

>- compilador Fortran (`numpy.distutils.fcompiler`)
>- compilador Rust (`cffi`+`milksnake`)
>- outro gerenciador de pacotes (`npm`)

. . . 

e criamos pacotes binários instaláveis pelo conda.
Tudo isso é possível no formato wheels com `cibuildwheels` ou `multibuild`.

. . . 

Mas esquecemos da Paula e da Tália!

# Paula

>- Missão: Comprimir terabytes de resultados de modelos atmosféricos.
>- Código: Paper e código fonte publicados na Nature computational Science em... `Julia`!
>- Operação: Aplicar essa compressão de dados em seus modelos pela `xarray`.
>- Dificuldade: Depender de outra linguagem no _runtime_!

# Julia é só outro pacote para o conda

```yaml
requirements:
  run:
    - python >=3.8
    - xarray
    - pyjulia >=0.5.7
    - julia >=1.6.0,<1.8.0
```

# Do outro lado do (Python)

```python
from .julia_helpers import install

already_ran = False
if not already_ran:
    already_ran = install(quiet=True)

jl = Julia(compiled_modules=False, debug=False)
from julia import Main

path_to_julia_functions = os.path.join(
    os.path.dirname(__file__), "bitinformation_wrapper.jl"
)
Main.path = path_to_julia_functions
jl.using("BitInformation")
```

# Tália

>- Missão: Ler os terabytes de resultados de modelos atmosféricos da Paula.
>- Código: Aprendendo Python mas precisa do `nco`,  ferramenta não-python oficial do IPCC
>- Operação: Usar o `nco` de dentro do Python para não sair da zona de conforto, `pynco`.
>- Dificuldade: Instalar um pacote CLI C/C++ que será chamado pelo `pynco`.


# nco é só outro pacote para o conda

```yaml
requirements:
  host:
    - python >=3.6
    - pip
  run:
    - python >=3.6
    - nco
    - numpy
```

# To the ~~moon~~ Web

[https://github.com/ocefpaf/ioos_qc_webapp](https://github.com/ocefpaf/ioos_qc_webapp)


![](images/qr-ioos_qc_webapp.png)


# and back

[https://github.com/ocean-data-qc/ocean-data-qc](https://github.com/ocean-data-qc/ocean-data-qc)

![](images/qr-atlantOS.png)


# Boas práticas: Core comum

[https://github.com/TEOS-10/GSW-C](https://github.com/TEOS-10/GSW-C)

![](images/qr-gsw-python-wheel.png)


# Boas práticas:  gerenciado apps com conda-lock

![](images/conda-lock.png)

[Image from PR 235](https://github.com/pangeo-data/pangeo-docker-images/pull/235).

# Perguntas?

(How To Make Package Managers Cry)

[https://www.youtube.com/watch?v=NSemlYagjIU](https://www.youtube.com/watch?v=NSemlYagjIU)

![](images/qr-youtube-pkg.png)
