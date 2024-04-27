import datetime

# Dicionário para armazenar usuários e senhas
usuarios_senhas = {
    "admin": "admin123",  # Exemplo de usuário e senha predefinidos
}

# Dicionário para armazenar histórico de cálculos de cada usuário
historico_calculos = {}

def calcular_imc(peso, altura_cm):
    altura_m = altura_cm / 100
    return peso / (altura_m ** 2)

def classificar_imc(imc, idade):
    if 0 <= idade <= 15:
        if imc < 18.5:
            return "Abaixo do peso", 18.5 - imc, 18.5, imc, 12, 15, 18
        elif 18.5 <= imc < 24.9:
            return "Peso normal", 0, 18.5, imc, 12, 15, 18
        elif 25 <= imc < 29.9:
            return "Sobrepeso", imc - 24.9, 18.5, imc, 12, 15, 18
        else:
            return "Obesidade", imc - 24.9, 18.5, imc, 12, 15, 18
    elif 15 < idade <= 60:
        if imc < 18.5:
            return "Abaixo do peso", 18.5 - imc, 18.5, imc, 18.5, 24.9, 29.9
        elif 18.5 <= imc < 24.9:
            return "Peso normal", 0, 18.5, imc, 18.5, 24.9, 29.9
        elif 25 <= imc < 29.9:
            return "Sobrepeso", imc - 24.9, 18.5, imc, 18.5, 24.9, 29.9
        else:
            return "Obesidade", imc - 24.9, 18.5, imc, 18.5, 24.9, 29.9
    elif 60 < idade <= 110:
        if imc < 23:
            return "Abaixo do peso", 23 - imc, 23, imc, 23, 28, 30
        elif 23 <= imc < 28:
            return "Peso normal", 0, 23, imc, 23, 28, 30
        elif 28 <= imc < 30:
            return "Sobrepeso", imc - 28, 23, imc, 23, 28, 30
        else:
            return "Obesidade", imc - 28, 23, imc, 23, 28, 30
    else:
        return "Faixa etária não suportada", 0, 0, 0, 0, 0, 0

def salvar_usuario(nome, senha):
    # Adiciona o novo usuário e senha ao dicionário
    usuarios_senhas[nome] = senha
    # Inicializa o histórico de cálculos do novo usuário
    historico_calculos[nome] = []

def carregar_usuario():
    return input("Digite seu nome de usuário: ")

def carregar_senha():
    return input("Digite sua senha: ")

def criar_usuario():
    nome = input("Digite um nome de usuário: ")
    senha = input("Digite uma senha: ")
    salvar_usuario(nome, senha)
    return nome

def autenticar_usuario():
    nome_usuario = carregar_usuario()

    # Se o usuário não existir, cria um novo
    if nome_usuario not in usuarios_senhas:
        print("Usuário não encontrado. Criando novo usuário...")
        nome_usuario = criar_usuario()
        return nome_usuario
    else:
        # Se o usuário existir, autentica
        senha = carregar_senha()
        if usuarios_senhas[nome_usuario] == senha:
            print(f"Bem-vindo de volta, {nome_usuario}!")
            return nome_usuario
        else:
            print("Senha incorreta. Tente novamente.")
            return None

def salvar_ultima_consulta(data, resultado):
    nome_usuario = carregar_usuario()
    historico = historico_calculos.get(nome_usuario, [])
    if len(historico) >= 5:
        historico.pop(0)  # Remove o cálculo mais antigo se o histórico estiver cheio
    historico.append((data, resultado))
    historico_calculos[nome_usuario] = historico

def exibir_historico():
    nome_usuario = carregar_usuario()
    historico = historico_calculos.get(nome_usuario, [])
    print("\nHistórico de consultas:")
    if historico:
        for data, resultado in historico:
            print(f"Data: {data}, Resultado: {resultado}")
    else:
        print("Nenhum registro encontrado.")

def calcular_e_exibir_imc():
    print("\nVamos calcular o seu IMC!")
    nome_usuario = carregar_usuario()
    if nome_usuario:
        data = datetime.date.today().strftime("%d/%m/%Y")
        peso = float(input("Digite o seu peso em kg: "))
        altura_cm = float(input("Digite a sua altura em cm: "))
        idade = int(input("Digite a sua idade: "))

        imc = calcular_imc(peso, altura_cm)
        classificacao, diferenca_ideal, imc_ideal, imc_resultado, minimo, medio, maximo = classificar_imc(imc, idade)

        print("\nResultado do IMC:")
        print(f"Data da consulta: {data}")
        print(f"IMC: {imc_resultado:.2f}")
        print(f"Classificação: {classificacao}")
        print(f"Diferença do IMC ideal: {diferenca_ideal:.2f}")
        print(f"IMC ideal: {imc_ideal:.2f}")
        print(f"Faixa de IMC ideal: {minimo:.2f} - {medio:.2f} - {maximo:.2f}")

        salvar_ultima_consulta(data, imc_resultado)

def menu_principal():
    while True:
        print("\nEscolha uma opção:")
        print("1. Calcular IMC")
        print("2. Histórico de consultas")
        print("3. Sair")
        opcao = input("Opção: ")

        if opcao == "1":
            calcular_e_exibir_imc()
        elif opcao == "2":
            exibir_historico()
        elif opcao == "3":
            print("Até logo!")
            break
        else:
            print("Opção inválida. Tente novamente.")

def menu_inicial():
    print("Bem-vindo ao Calculador de IMC, criado por Jefferson Jaily Felix!\n")
    print("Escolha uma opção:")
    print("1. Entrar")
    print("2. Novo usuário")
    print("3. Sair")

def main():
    while True:
        menu_inicial()
        opcao = input("Opção: ")

        if opcao == "1":
            nome_usuario = autenticar_usuario()
            if nome_usuario:
                menu_principal()
        elif opcao == "2":
            criar_usuario()
        elif opcao == "3":
            print("Até logo!")
            break
        else:
            print("Opção inválida. Tente novamente.")

if __name__ == "__main__":
    main()
