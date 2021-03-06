
<h1 align="center">
  <br>
  <a href="https://pypi.org/project/pysigep/">
  <img src="https://raw.githubusercontent.com/mstuttgart/pycep-correios/develop/docs/_static/logo.jpg" width="30%"></a>
  <br>
  PySIGEP
  <br>
</h1>

<h4 align="center">Interface python para uso dos serviços fornecidos pelo SIGEPWeb dos Correios.</h4>

<p align="center">
  <a href="#sobre">Sobre</a> |
  <a href="#recursos">Recursos</a> |
  <a href="#instalação">Instalação</a> |
  <a href="#utilização">Utilização</a> |
  <a href="#como-contribuir">Como Contribuir</a> |
  <a href="#créditos">Créditos</a>
</p>


## Sobre

O SIGEP WEB é um sistema com o propósito de preparar e gerenciar
as postagens de Clientes dos Correios. Seus principais atributos técnicos são:
facilidade e rapidez na preparação das postagens e gestão das informações sobre os objetos postados.

## Recursos

-  Verificar *status* de um Cartão de Postagem
-  Obter dados do endereço a partir de seu respectivo CEP.
-  Verificar disponibilidade de um dado serviço.
-  Gerar etiquetas para postagem de mercadoria.

## Instalação

A versão atual **ainda esta em fase de desenvolvimento**, sendo que os recursos disponiveis podem ser removidos sem aviso prévio. Portanto, não é recomendável seu uso em ambiente de produção. 

Atualmente, a PySIGEP possui suporte para Python 3.5+.

> pip install pysigep

#### A partir do código fonte

Você pode realizar o *clone* do repositório público:

> git clone https://github.com/mstuttgart/pysigep.git

Uma vez que você tenha uma cópia do código fonte, você pode instalá-lo com:

> python setup.py install

## Utilização

A versão atual **ainda esta em fase de desenvolvimento**, sendo que os recursos disponiveis podem ser removidos sem aviso prévio. Portanto, não é recomendável
seu uso em ambiente de produção.

PySigep no momento possui suporte para os seguintes serviços providos pelo SigepWeb:

* consultaCEP
* verificaDisponibilidadeServico
* getStatusCartaoPostagem
* solicitaEtiquetas
* geraDigitoVerificadorEtiquetas

Novos serviços serão implementados futuramente. A seguir temos alguns exemplos de utilização da `pysigep`. Para mais detalhes sobre os serviçoes fornecidos, por favor, consulte o Manual do SigepWeb.

#### SOAPClient

A grande maioria dos serviços do SigepWeb, exigem um cadastro de *usuário* e *senha*. De modo a tornar mais prático a consulta, foi desenvolvido uma classe para armazenar estes dados durante o uso da biblioteca. Sendo assim, antes de qualquer consulta devemos criar um objeto `SOAPClient`.

```python
from pysigep.utils import HOMOG_USUARIO, HOMOG_SENHA, HOMOLOGACAO

    # Criamos o cliente SOAP
    cliente = SOAPClient(ambiente=HOMOLOGACAO,
                         senha=HOMOG_SENHA,
                         usuario=HOMOG_USUARIO)

    # Realizamos a consulta di servico
    endereco = cliente.nomeservico(..)
```

As constantes `HOMOG_USUARIO`, `HOMOG_SENHA`, `HOMOLOGACAO` são constantes fornecidas para auxiliar o desenvolvedor durante o processo de desenvolvimento. 

No momento, a `pysigep` possui as seguintes constantes:

* HOMOG_USUARIO: usuario para consultas em ambiente de homologação,
* HOMOG_SENHA: senha para o usuário de homologação,
* HOMOLOGACAO: constante a ser fornecida na criação do `SOAPClient`, permitindo utilizar o ambiente de homologação,
* PRODUCAO: constante a ser fornecida na criação do `SOAPClient`, permitindo utilizar o ambiente de homologação,
* HOMOG_CODIGO_ADMIN: código adminstrativo de demonstração, fornecido pelos correios,
* HOMOG_CARTAO: código do cartão de postagem de demonstração, fornecido pelos correios,
* HOMOG_CNPJ: CNPJ de demonstração, fornecido pelos correios,
* URLS: *dict* contendo as urls dos ambientes de homologação e produção.

Estas constantes podem ser acessadas através do pacote `utils`:

```python

    from pysigep.utils import (URLS,
                               HOMOLOGACAO,
                               PRODUCAO
                               HOMOG_USUARIO,
                               HOMOG_SENHA,
                               HOMOG_CODIGO_ADMIN,
                               HOMOG_CARTAO,
                               HOMOG_CNPJ)
```

#### consultaCEP

Este método retorna o endereço correspondente ao número de CEP informado.

```python

    # Criamos o cliente SOAP
    cliente = SOAPClient(ambiente=HOMOLOGACAO,
                         senha=HOMOG_SENHA,
                         usuario=HOMOG_USUARIO)

    # Realizamos a consulta de CEP
    endereco = cliente.consulta_cep('37.503-130')

    print(endereco.bairro)
    print(endereco.cep)
    print(endereco.cidade)
    print(endereco.complemento)
    print(endereco.complemento2)
    print(endereco.end)
    print(endereco.id)
    print(endereco.uf)
    print(endereco.unidadesPostagem)
```

#### verificaDisponibilidadeServico

Verifica se um serviço que não possui abrangência nacional está disponível entre um CEP de Origem e de Destino.

```python

    params = {
        'cod_administrativo': HOMOG_CODIGO_ADMIN,
        'numero_servico': '04162',
        'cep_origem': '70002900',
        'cep_destino': '70.002-900',
    }

    # Criamos o cliente SOAP
    cliente = SOAPClient(ambiente=HOMOLOGACAO,
                         senha=HOMOG_SENHA,
                         usuario=HOMOG_USUARIO)

    # Realizamos a verificacao de disponibilidade
    disponibilidade = cliente.verifica_disponibilidade_servico(**params)

    print(disponibilidade)
    # Saída: True ou False
```

#### getStatusCartaoPostagem

Este método retorna o situação do cartão de postagem, ou seja, se o mesmo está 'Normal' ou 'Cancelado'. 
É recomendada a pesquisa periódica para evitar tentativa de postagens com cartão suspenso, ocasionando
a não aceitação dos objetos nos Correios.

```python

    # Criamos o cliente SOAP
    cliente = SOAPClient(ambiente=HOMOLOGACAO,
                         senha=HOMOG_SENHA,
                         usuario=HOMOG_USUARIO)

    params = {
        'numero_cartao_postagem': HOMOG_CARTAO,
    }

    # Realizamos a consulta do status do cartao de postagem
    status = cliente.get_status_cartao_postagem(**params)

    print(status)
    # Saída: 'Normal' ou 'Cancelado'
```

#### solicitaEtiquetas

Retorna uma dada quantidade de etiquetas sem o digito verificador.

```python

    params = {
        'tipo_destinatario': 'C',
        'cnpj': HOMOG_CNPJ,
        'id_servico': 124849,
        'qtd_etiquetas': 2,
    }

    # Criamos o cliente SOAP
    cliente = SOAPClient(ambiente=HOMOLOGACAO,
                         senha=HOMOG_SENHA,
                         usuario=HOMOG_USUARIO)

    params = {
        'numero_cartao_postagem': HOMOG_CARTAO,
    }

    # Realizamos a solicitacao de etiquetas
    lista_etiquetas = cliente.solicita_etiquetas(**params)

    print(lista_etiquetas)
    # Saída: lista_etiquetas = [
    #             'DL76023727 BR',
    #             'DL76023728 BR',
    #        ]

```
#### geraDigitoVerificadorEtiquetas

Este método retorna o DV - Dígito Verificador de um lista de etiquetas.

```python

    params = {
            'etiquetas': ['DL76023727 BR', 'DL76023728 BR'],
    }

    # Criamos o cliente SOAP
    cliente = SOAPClient(ambiente=HOMOLOGACAO,
                         senha=HOMOG_SENHA,
                         usuario=HOMOG_USUARIO)

    # Realizamos a consulta do status do cartao de postagem
    lista_digitos = cliente.gera_digito_verificador_etiquetas(**params)

    print(lista_digitos)
    # Saída: lista_digitos = [2, 6]
```

