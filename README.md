# Escola-lista-8
class Turma:
    def __init__(self, nome, segmento, curso, ano):
        self._nome = nome
        self._segmento = segmento
        self._curso = curso
        self._ano = ano
        self.alunos = []
        self.professores = []

    @property
    def nome(self):
        return self._nome

    @nome.setter
    def nome(self, novo_nome):
        if not novo_nome.strip():
            raise ValueError("O nome da turma não pode ser vazio.")
        self._nome = novo_nome

    @property
    def curso(self):
        return self._curso

    @curso.setter
    def curso(self, novo_curso):
        if not novo_curso.strip():
            raise ValueError("O curso da turma não pode ser vazio.")
        self._curso = novo_curso

    def adicionar_aluno(self, aluno):
        self.alunos.append(aluno)

    def remover_aluno(self, ra):
        self.alunos = [aluno for aluno in self.alunos if aluno.ra != ra]

    def adicionar_professor(self, professor):
        self.professores.append(professor)

    def remover_professor(self, cpf):
        self.professores = [prof for prof in self.professores if prof.cpf != cpf]

    def validar_turma(self):
        if self._segmento == "EM":
            return len(self.alunos) >= 20
        elif self._segmento == "Superior":
            return len(self.alunos) >= 5
        return False

    @property
    def detalhes(self):
        return {
            "nome": self._nome,
            "segmento": self._segmento,
            "curso": self._curso,
            "ano": self._ano,
            "num_alunos": len(self.alunos),
            "num_professores": len(self.professores)
        }


class Pessoa:
    def __init__(self, primeiro_nome, sobrenome, endereco, email):
        self._primeiro_nome = primeiro_nome
        self._sobrenome = sobrenome
        self._endereco = endereco
        self._email = email

    @property
    def primeiro_nome(self):
        return self._primeiro_nome

    @primeiro_nome.setter
    def primeiro_nome(self, novo_nome):
        if not novo_nome.strip():
            raise ValueError("O primeiro nome não pode ser vazio.")
        self._primeiro_nome = novo_nome

    @property
    def email(self):
        return self._email

    @email.setter
    def email(self, novo_email):
        if "@" not in novo_email:
            raise ValueError("Email inválido.")
        self._email = novo_email


class Aluno(Pessoa):
    def __init__(self, primeiro_nome, sobrenome, endereco, email, ra, segmento, curso, turma):
        super().__init__(primeiro_nome, sobrenome, endereco, email)
        self.ra = ra
        self.segmento = segmento
        self.curso = curso
        self.turma = turma

    @property
    def detalhes(self):
        return {
            "nome": f"{self.primeiro_nome} {self._sobrenome}",
            "endereco": self._endereco,
            "email": self._email,
            "ra": self.ra,
            "segmento": self.segmento,
            "curso": self.curso,
            "turma": self.turma.nome if self.turma else None
        }


class Professor(Pessoa):
    def __init__(self, primeiro_nome, sobrenome, endereco, email, cpf, formacao):
        super().__init__(primeiro_nome, sobrenome, endereco, email)
        self.cpf = cpf
        self.formacao = formacao
        self.turmas = []

    @property
    def detalhes(self):
        return {
            "nome": f"{self.primeiro_nome} {self._sobrenome}",
            "endereco": self._endereco,
            "email": self._email,
            "cpf": self.cpf,
            "formacao": self.formacao,
            "turmas": [turma.nome for turma in self.turmas]
        }


def selecionar_curso(segmento):
    cursos_em = ["Informática", "Mecatrônica", "Eletromecânica"]
    cursos_superior = ["Bacharel em Ciências da Computação", "Bacharel em Pedagogia"]

    cursos = cursos_em if segmento == "EM" else cursos_superior

    print("Opções de cursos disponíveis:")
    for i, curso in enumerate(cursos, 1):
        print(f"{i}. {curso}")

    while True:
        escolha = input("Escolha o curso pelo número: ").strip()
        if escolha.isdigit() and 1 <= int(escolha) <= len(cursos):
            return cursos[int(escolha) - 1]
        print("Escolha inválida. Tente novamente.")


def menu():
    turmas = []

    while True:
        print("\n=== Menu Principal ===")
        print("1. Adicionar dados")
        print("2. Remover dados")
        print("3. Editar dados")
        print("4. Listar turmas")
        print("5. Sair")

        opcao = input("Escolha uma opção: ").strip()

        if opcao == "1":
            adicionar_dados(turmas)
        elif opcao == "2":
            remover_dados(turmas)
        elif opcao == "3":
            editar_dados(turmas)
        elif opcao == "4":
            listar_turmas(turmas)
        elif opcao == "5":
            print("Encerrando o programa...")
            break
        else:
            print("Opção inválida. Tente novamente.")


def adicionar_dados(turmas):
    print("\n=== Adicionar Dados ===")
    tipo = input("Deseja adicionar um aluno, professor ou turma? (aluno/professor/turma): ").strip().lower()

    if tipo == "aluno":
        primeiro_nome = input("Primeiro nome do aluno: ")
        sobrenome = input("Sobrenome do aluno: ")
        endereco = input("Endereço do aluno: ")
        email = input("Email do aluno: ")
        ra = input("RA do aluno: ")
        segmento = input("Segmento (EM/Superior): ").strip()
        curso = selecionar_curso(segmento)
        turma_nome = input("Nome da turma: ")
        
        turma = next((t for t in turmas if t.nome == turma_nome), None)
        if not turma:
            print("Turma não encontrada. Crie a turma primeiro.")
            return

        aluno = Aluno(primeiro_nome, sobrenome, endereco, email, ra, segmento, curso, turma)
        turma.adicionar_aluno(aluno)
        print(f"Aluno {primeiro_nome} adicionado à turma {turma.nome}.")

    elif tipo == "professor":
        primeiro_nome = input("Primeiro nome do professor: ")
        sobrenome = input("Sobrenome do professor: ")
        endereco = input("Endereço do professor: ")
        email = input("Email do professor: ")
        cpf = input("CPF do professor: ")
        formacao = input("Formação do professor: ")

        professor = Professor(primeiro_nome, sobrenome, endereco, email, cpf, formacao)
        print(f"Professor {primeiro_nome} cadastrado com sucesso.")

    elif tipo == "turma":
        nome = input("Nome da turma: ")
        segmento = input("Segmento (EM/Superior): ")
        curso = selecionar_curso(segmento)
        ano = input("Ano da turma: ")
        turma = Turma(nome, segmento, curso, ano)
        turmas.append(turma)
        print(f"Turma {nome} criada com sucesso.")

    else:
        print("Tipo inválido.")


def remover_dados(turmas):
    print("\n=== Remover Dados ===")
    tipo = input("Deseja remover um aluno, professor ou turma? (aluno/professor/turma): ").strip().lower()

    if tipo == "aluno":
        ra = input("RA do aluno a ser removido: ")
        for turma in turmas:
            if any(aluno.ra == ra for aluno in turma.alunos):
                turma.remover_aluno(ra)
                print(f"Aluno com RA {ra} removido da turma {turma.nome}.")
                return
        print("Aluno não encontrado.")

    elif tipo == "professor":
        cpf = input("CPF do professor a ser removido: ")
        for turma in turmas:
            if any(prof.cpf == cpf for prof in turma.professores):
                turma.remover_professor(cpf)
                print(f"Professor com CPF {cpf} removido.")
                return
        print("Professor não encontrado.")

    elif tipo == "turma":
        nome = input("Nome da turma a ser removida: ")
        turma = next((t for t in turmas if t.nome == nome), None)
        if turma:
            turmas.remove(turma)
            print(f"Turma {nome} removida com sucesso.")
        else:
            print("Turma não encontrada.")

    else:
        print("Tipo inválido.")


def editar_dados(turmas):
    print("\n=== Editar Dados ===")
    tipo = input("Deseja editar um aluno, professor ou turma? (aluno/professor/turma): ").strip().lower()

    if tipo == "aluno":
        ra = input("RA do aluno a ser editado: ")
        for turma in turmas:
            aluno = next((a for a in turma.alunos if a.ra == ra), None)
            if aluno:
                novo_email = input(f"Novo email (atual: {aluno.email}): ")
                aluno.email = novo_email
                print("Dados do aluno atualizados.")
                return
        print("Aluno não encontrado.")

    elif tipo == "professor":
        cpf = input("CPF do professor a ser editado: ")
        for turma in turmas:
            professor = next((p for p in turma.professores if p.cpf == cpf), None)
            if professor:
                novo_endereco = input(f"Novo endereço (atual: {professor._endereco}): ")
                professor._endereco = novo_endereco
                print("Dados do professor atualizados.")
                return
        print("Professor não encontrado.")

    elif tipo == "turma":
        nome = input("Nome da turma a ser editada: ")
        turma = next((t for t in turmas if t.nome == nome), None)
        if turma:
            novo_nome = input(f"Novo nome da turma (atual: {turma.nome}): ")
            turma.nome = novo_nome
            print("Dados da turma atualizados.")
        else:
            print("Turma não encontrada.")

    else:
        print("Tipo inválido.")


def listar_turmas(turmas):
    print("\n=== Listar Turmas ===")
    if not turmas:
        print("Nenhuma turma cadastrada.")
        return

    for turma in turmas:
        detalhes = turma.detalhes
        print(f"\nTurma: {detalhes['nome']}")
        print(f"Segmento: {detalhes['segmento']}")
        print(f"Curso: {detalhes['curso']}")
        print(f"Ano: {detalhes['ano']}")
        print(f"Número de alunos: {detalhes['num_alunos']}")
        print(f"Número de professores: {detalhes['num_professores']}")


if __name__ == "__main__":
    menu()
