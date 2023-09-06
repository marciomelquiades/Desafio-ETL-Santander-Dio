{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "provenance": [],
      "authorship_tag": "ABX9TyPntWobcnTC+DbQuIs/x339",
      "include_colab_link": true
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    },
    "language_info": {
      "name": "python"
    }
  },
  "cells": [
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "view-in-github",
        "colab_type": "text"
      },
      "source": [
        "<a href=\"https://colab.research.google.com/github/marciomelquiades/Desafio-ETL-Santander-Dio/blob/main/README.md\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "# Utilize sua própria URL se quiser ;)\n",
        "# Repositório da API: https://github.com/digitalinnovationone/santander-dev-week-2023-api\n",
        "sdw2023_api_url = 'https://sdw-2023-prd.up.railway.app'"
      ],
      "metadata": {
        "id": "aiBs_nnHzXps"
      },
      "execution_count": 19,
      "outputs": []
    },
    {
      "cell_type": "code",
      "execution_count": 20,
      "metadata": {
        "id": "C6K8i_-wp2d-"
      },
      "outputs": [],
      "source": [
        "import pandas as pd\n"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "df = pd.read_csv('SDW2023.csv')\n",
        "user_ids = df['UserID'].tolist()\n",
        "print(user_ids)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "HeY1t33HxPRu",
        "outputId": "96e53709-3a4d-447e-c8b6-dbc08c966afe"
      },
      "execution_count": 21,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "[1637, 1641, 1642]\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "import requests\n",
        "import json"
      ],
      "metadata": {
        "id": "C0dj8aifxqeD"
      },
      "execution_count": 22,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "def get_user(id):\n",
        "  response = requests.get(f'{sdw2023_api_url}/users/{id}')\n",
        "  return response.json() if response.status_code == 200 else None\n",
        "\n",
        "users = [user for id in user_ids if (user := get_user(id)) is not None]\n",
        "print(json.dumps(users, indent=2))"
      ],
      "metadata": {
        "id": "hMZV89eh2R9N"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "!pip install openai"
      ],
      "metadata": {
        "id": "fRw1d0xK2Pgc"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "openai_api_key = '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'"
      ],
      "metadata": {
        "id": "tZ1SJr7g3LAj"
      },
      "execution_count": 29,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "import openai\n",
        "\n",
        "openai.api_key = openai_api_key"
      ],
      "metadata": {
        "id": "I7yBR1zb4Ilb"
      },
      "execution_count": 31,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "def generate_ai_news(user):\n",
        "  completion = openai.ChatCompletion.create(\n",
        "  model=\"gpt-3.5-turbo\",\n",
        "  messages=[\n",
        "    {\"role\": \"system\",\n",
        "     \"content\": \"Você é um especialista em Customner Success Bancário\"\n",
        "     },\n",
        "    {\n",
        "        \"role\":\n",
        "        \"user\", \"content\": f\"Crie uma mensagem para {user['name']} sobre novas formas de investimentos (máximo de 100 caracteres)\"\n",
        "      }\n",
        "    ]\n",
        "  )\n",
        "  return completion.choices[0].message.content.strip('\\\"')\n",
        "\n",
        "for user in users:\n",
        "  news = generate_ai_news(user)\n",
        "  print(news)\n",
        "  user['news'].append({\n",
        "      \"icon\": \"https://digitalinnovationone.github.io/santander-dev-week-2023-api/icons/credit.svg\",\n",
        "      \"description\": news\n",
        "  })\n",
        "\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "ZgjKtfg44Rsi",
        "outputId": "0cd78119-de66-4b9d-bf8b-f19e7a359c0a"
      },
      "execution_count": 38,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Descubra oportunidades de investimento inovadoras!\n",
            "Diversifique seus investimentos! Descubra novas opções lucrativas.\n",
            "Descubra oportunidades de investimento inovadoras!\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "def update_user(user):\n",
        "  response = requests.put(f\"{sdw2023_api_url}/users/{user['id']}\", json=user)\n",
        "  return True if response.status_code == 200 else False\n",
        "\n",
        "for user in users:\n",
        " success = update_user(user)\n",
        " print(f\"User {user['name']} updated? {success}!\")\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "tQ1M9pRk7QQB",
        "outputId": "c931e77b-2e7d-4146-d5a7-caa377ba76f6"
      },
      "execution_count": 45,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "User Marcio updated? True!\n",
            "User Marcos updated? True!\n",
            "User Mauricio updated? True!\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [],
      "metadata": {
        "id": "DKk6oX47-RUf"
      },
      "execution_count": null,
      "outputs": []
    }
  ]
}