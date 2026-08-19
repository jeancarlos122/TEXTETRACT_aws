# TEXTTRACT_aws

AWS Textract — Reconhecimento de Listas

Projeto em Python que utiliza o Amazon Textract para reconhecer textos presentes em imagens e transformar uma lista fotografada ou digitalizada em dados que podem ser utilizados por outros sistemas.


Sobre o projeto

A proposta é automatizar a leitura de listas.

O usuário fornece uma imagem contendo uma lista, por exemplo:

Lista de Compras

1 - Arroz
2 - Feijão
3 - Óleo de soja
4 - Açúcar
5 - Café
6 - Leite
7 - Pão de forma
8 - Ovos

O sistema envia a imagem para o Amazon Textract, que realiza o reconhecimento óptico de caracteres (OCR).

🎯 Objetivo

O projeto tem como objetivo servir como uma base para sistemas capazes de:

📷 Receber imagens de listas
🔎 Reconhecer automaticamente os textos
📝 Extrair os itens presentes na imagem
🔢 Identificar quantidades
📊 Organizar os dados em tabelas
📁 Exportar os resultados para CSV ou Excel

🏗️ Tecnologias utilizadas
Python
AWS SDK for Python (Boto3)
Amazon Textract
AWS IAM
Amazon Web Services (AWS)
📁 Estrutura do projeto

Uma estrutura inicial recomendada:


aws-textract-list-reader/
│
├── images/
│   └── lista_exemplo.jpg
│
├── src/
│   └── textract_lista.py
│
├── .gitignore
├── README.md
└── requirements.txt


1. Instale o Boto3
pip install boto3


Configure suas credenciais AWS:

aws configure

Você vai informar:

AWS Access Key ID
AWS Secret Access Key
Default region name: us-east-1
Default output format: json
2. Código
import boto3
import sys




def reconhecer_lista(caminho_imagem):
    # Cria conexão com o Amazon Textract
    textract = boto3.client("textract", region_name="us-east-1")


     Abre a imagem
    with open(caminho_imagem, "rb") as arquivo:
        imagem = arquivo.read()


     Envia a imagem para o Textract
    resposta = textract.detect_document_text(
        Document={
            "Bytes": imagem
        }
    )


    itens = []


    # Percorre o resultado
    for bloco in resposta["Blocks"]:
        if bloco["BlockType"] == "LINE":
            texto = bloco["Text"].strip()


            if texto:
                itens.append(texto)


    return itens




if __name__ == "__main__":


    if len(sys.argv) < 2:
        print("Uso: python textract_lista.py imagem.jpg")
        sys.exit(1)


    caminho = sys.argv[1]


    try:
        lista = reconhecer_lista(caminho)


        print("\n===== ITENS RECONHECIDOS =====\n")


        for numero, item in enumerate(lista, start=1):
            print(f"{numero}. {item}")


        print("\n==============================")


    except Exception as erro:
        print(f"Erro ao processar imagem: {erro}")
        
3. Como usar

Por exemplo, você tem:

lista.jpg

Execute:

python textract_lista.py lista.jpg

Se a imagem tiver:

Lista de materiais


10 - Cimento
5 - Areia
20 - Tijolos
3 - Tubos PVC
2 - Cola

O programa retornará:

===== ITENS RECONHECIDOS =====


1. Lista de materiais
2. 10 - Cimento
3. 5 - Areia
4. 20 - Tijolos
5. 3 - Tubos PVC
6. 2 - Cola


==============================
Mas podemos deixar bem melhor

Se o seu objetivo é uma lista real, eu recomendo fazer o programa reconhecer também quantidade + produto.

Por exemplo:

10 Cimento
5 Areia
20 Tijolos
3 Tubo PVC

e transformar automaticamente em:

[
    {
        "quantidade": 10,
        "item": "Cimento"
    },
    {
        "quantidade": 5,
        "item": "Areia"
    },
    {
        "quantidade": 20,
        "item": "Tijolos"
    },
    {
        "quantidade": 3,
        "item": "Tubo PVC"
    }
]

Também dá para transformar isso em uma página web, onde você teria um botão:

📷 Enviar imagem

→ envia para o AWS Textract
→ reconhece a lista
→ mostra uma tabela:

