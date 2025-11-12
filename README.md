#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <unistd.h>
#include <windows.h>
#ifdef _WIN32
#else
#endif

//~~~(structs)~~~//
typedef struct
{
    char *Nome;
    char *TipoJutsu;
    char **Elementos;
    int QuantidadeDeElementos;
    int CustodeChakra;
    int PoderdeAtaque;
} tJutsu;

typedef struct
{
    char *NomeDoCla;
    tJutsu **JutsusHereditarios;
    int QuantidadeDeJutsusHereditarios;
    tJutsu **JutsusTradicionais;
    int QuantidadeDeJutsusTradicionais;
} tCla;

typedef struct
{
    char *Nome;
    tCla *CaracteristicasDoCla;
    char *Patente;
    int QuantidadeDeChakra;
    char *ElementoPrincipal;
    tJutsu **CaracteristicasDoJutsu;
    int QuantidadeDeJutsus;
} tNinja;

typedef struct
{
    char *Titulo;
    tNinja *LiderResponsavel;
    char *NivelDificuldade;
    char *Status;
    int QuantidadeDeNinjas;
    tNinja **NinjasDaMissao;
} tMissao;

void SalvaDados();
void RecuperaDados();

//~~~(protótipos / assinaturas de função)~~~//
void SelecionadorDeMenu(int TipoDeMenu);                  // 1. Seleciona o Tipo de Menu Que Sera Impresso;
void SelecionarContexto(int TipoDeContexto);              // 2. Seleciona o Contexto Com a Opçao do Menu Inical;
void SelecionarAçao(int TipoDeContexto, int OpçaoDaAçao); // 3. Seleciona a Açao de Acordo com o Contexto Selecionado no Menu Inical e a Opçao Selecionada no Menu do Contexto Selecionado;
int NovoCadastro(int TipoDeCadastro, int TipoDeCaso);     // 4. Seleciona o Tipo de Criar de Acordo com o Tipo de Cadastro, a Maneira de Cadastro Varia de Acordo Com o Tipo Caso;
void Imprime(int CasoDeImpressao, int TipoDeImpressao);   // 5. Seleciona o Tipo de Impressao de Acordo Com a Opçao Selecionada(Shinobi,Cla,Jutsu,Missoa) e Varia de Acordo Com o Caso de Impressao;

void limpar_tela(void);   // 6. Seleciona o Modo Para Limpar o Terminal de Acordo Com o Sistema OP;
void congelar_tela(void); // 7. Seleciona o Modo Para Congelar o Terminal de Acordo Com o Sistema Op;
void Carregamento();
int simOUnao(void);                                                        // 8. Scaneia e Verifica Resposta de SIM ou NAO, caso a resposta seja 'SIM', a funçao retorna 1, se a resposta for 'NAO' a funçao retorna 0;
int DesejaInformaçoes(int informaçao);                                     // 10. Seleciona e Administra as funçoes: 'Explicaçoes' e 'Exemplos' de Acordo com o Codigo de Cada Explicaçao e Cada Exemplo, Tudo isso em Relaçao a Opçao que he Deseja, Entre '{ 1 } - Explicaçao' e '{ 2 } - Exemplo';
void Explicaçoes(int informaçao);                                          // 11. Seleciona o Tipo de Explicaçao De Acordo Com o Codigo Recebido da Funçao 'DesejaInformaçoes';
void Exemplos(int informaçao);                                             // 12. Seleciona o Tipo de Exemplo De Acordo Com o Codigo Recebido da Funçao 'DesejaInformaçoes';
void SelecionaStatus(int Opçao, char StrAux[100]);                         // 13. Seleciona o Status da Missao de Acordo com o Numero Recebido da Funçao 'VereficaQuantia'
void SelecionaPatente(int Opçao, char StrAux[100]);                        // 14. Seleciona a Patente da Missao de Acordo com o Numero Recebido da Funçao 'VereficaQuantia'
void RelatorioDaOpçao(int Contexto, int OpçaoDeContexto, int Necessidade); // 15. Seleciona e Imprime o Relatorio de Acordo Com o Contexto(Shinobi,Cla,Jutsu,Missao), E Administra as Variaveis Globais de Acordo Com a Opçao Selecionada Na Funçao 'Imprime';
int EscolheOpçao(int Contexto);
void AlterarMissaoEspecifica(tMissao *missao);
void Remover(int TipoDeContexto);
void Alterar(int TipoDeContexto);
void AlterarJutsuEspecifico(tJutsu *jutsu);
void ExibirDadosAtuais(int TipoDeContexto, int indice);
void RemoverAtributosEspecificos(int TipoDeContexto, int indice);

void VerificaNivelDeDificuldade(char StrAuX[100]);         // 16. Seleciona e Verifica o Nivel de Dificuldade das Missoes(S,A,B,C,D);
void VerificadorDeTexto(char Texto[100]);                  // 17. Scaneia Um Texto e Verifica Se Nao Tem Numeros Nele;
int VereficaQuantia(int QuantiaMinima, int QuantiaMaxima); // 18. Scaneia Um Char e Verifica Se Nao Tem Letras Nele, Depois Converte Ele em Int e Verefica se Esta de Acordo Com A 'QuantidadeMinima' e a 'QuantidadeMaxima';
int VerficaRepetiçaoDeOpçao(int ValordoI, int ValorMaximoDeOpçoes, int *aux, int CodigoDigitado);

tNinja CriarShinobi(void); // 19. Complementa a Funçao 'NovoCadastro' e Manipula Todas as Outras Funçoes(Quando Necessario), Enquanto Cadastra Shinobi;
tCla CriarCla(void);       // 20. Complementa a Funçao 'NovoCadastro' e Manipula Todas as Outras Funçoes(Quando Necessario), Enquanto Cadastra Shinobi;
tJutsu CriarJutsu(void);   // 21. Complementa a Funçao 'NovoCadastro' e Manipula Todas as Outras Funçoes(Quando Necessario), Enquanto Cadastra Shinobi;
tMissao CriarMissao(void); // 22. Complementa a Funçao 'NovoCadastro' e Manipula Todas as Outras Funçoes(Quando Necessario), Enquanto Cadastra Shinobi;

//~~~(variáveis globais)~~~//
tNinja *_ConjuntoDeShinobis;
int _NumeroDoShinobi = 0;

tJutsu *_ConjuntoDeJutsus;
int _NumeroDoJutsu = 0;

tCla *_ConjuntoDeClas;
int _NumeroDoCla = 0;

tMissao *_ConjuntoDeMissoes;
int _NumeroDaMissao = 0;

//~~~(main)~~~//
int main()
{
    int op = 1;
    int Inicial = 0;
    RecuperaDados();
    while (1)
    {
        SelecionadorDeMenu(Inicial);
        printf("Escolha uma opção: ");
        op = VereficaQuantia(1, 5);
        if (op == 5)
        {
            SalvaDados();
            Carregamento();
            break;
        }
        Carregamento();
        limpar_tela();
        SelecionarContexto(op);
    }
    return 0;
}

//~~~(implementações das funções)~~~//

void SelecionarContexto(int TipoDeContexto)
{
    int op = 1;
    while (op != 5)
    {
        SelecionadorDeMenu(TipoDeContexto);
        printf("Escolha uma opção: ");
        op = VereficaQuantia(1, 5);
        Carregamento();
        limpar_tela();
        SelecionarAçao(TipoDeContexto, op);
    }
}

void SelecionarAçao(int TipoDeContexto, int OpçaoDaAçao)
{
    switch (TipoDeContexto)
    {
    case 1:
        NovoCadastro(OpçaoDaAçao, 1);
        break;

    case 2:
        Imprime(1, OpçaoDaAçao);
        break;

    case 3:
        Alterar(OpçaoDaAçao);
        break;

    case 4:
        Remover(OpçaoDaAçao);
        break;
    }
}

int NovoCadastro(int TipoDeCadastro, int TipoDeCaso)
{
    //  1 - Para Cadastros Padrao;
    //  2 - Para Cadastros Que Cadastra Um Novo Contexto QUando o Ususario Digita { 0 } em Opçoes;
    switch (TipoDeCadastro)
    {
    case 1:
        if (_NumeroDoShinobi == 0)
        {
            printf("\nNao a Nenhum Shinobi Cadastrado no Momento!\nDeseja Cadastrar Um Novo Shinobi?(s/n) ");
            if (simOUnao() == 1)
            {
                _ConjuntoDeShinobis = (tNinja *)malloc(1 * sizeof(tNinja));
                _ConjuntoDeShinobis[_NumeroDoShinobi] = CriarShinobi();
                _NumeroDoShinobi++;
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nShinobi Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                congelar_tela();
            }
            else
            {
                return -1;
            }
        }
        else
        {
            if (TipoDeCaso == 1)
            {
                printf("Deseja Adicionar Mais Algum Shinobi?(s/n) ");
                if (simOUnao() == 1)
                {
                    _ConjuntoDeShinobis = (tNinja *)realloc(_ConjuntoDeShinobis, (_NumeroDoShinobi + 1) * sizeof(tNinja));
                    _ConjuntoDeShinobis[_NumeroDoShinobi] = CriarShinobi();
                    _NumeroDoShinobi++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nShinobi Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                }
            }
            else if (TipoDeCaso == 2)
            {
                int OpçaoDeShinobi;
                int NovoShinobi = 0;
                printf("\n{ 0 } - Cadastrar Novo Shinobi\nEscolha uma opção: ");
                OpçaoDeShinobi = VereficaQuantia(NovoShinobi, (_NumeroDoShinobi));
                if (OpçaoDeShinobi == NovoShinobi)
                {
                    _ConjuntoDeShinobis = (tNinja *)realloc(_ConjuntoDeShinobis, (_NumeroDoShinobi + 1) * sizeof(tNinja));
                    _ConjuntoDeShinobis[_NumeroDoShinobi] = CriarShinobi();
                    _NumeroDoShinobi++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nShinobi Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                    return _NumeroDoShinobi;
                }
                else if (OpçaoDeShinobi != 0)
                {
                    return OpçaoDeShinobi;
                }
            }
        }
        break;

    case 2:
    {
        if (_NumeroDoCla == 0)
        {
            printf("\nNao a Nenhum Cla Cadastrado no Momento!\nDeseja Cadastrar Um Novo Cla?(s/n) ");
            if (simOUnao() == 1)
            {
                _ConjuntoDeClas = (tCla *)malloc(1 * sizeof(tCla));
                _ConjuntoDeClas[_NumeroDoCla] = CriarCla();
                _NumeroDoCla++;
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCla Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                congelar_tela();
            }
            else
            {
                return -1;
            }
        }
        else
        {
            if (TipoDeCaso == 1)
            {
                printf("Deseja Adicionar Mais Algum Cla?(s/n) ");
                if (simOUnao() == 1)
                {
                    _ConjuntoDeClas = (tCla *)realloc(_ConjuntoDeClas, (_NumeroDoCla + 1) * sizeof(tCla));
                    _ConjuntoDeClas[_NumeroDoCla] = CriarCla();
                    _NumeroDoCla++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCla Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                }
            }
            else if (TipoDeCaso == 2)
            {
                int OpçaoDeCla;
                int NovoCla = 0;
                printf("\n{ 0 } - Cadastrar Novo Cla\nEscolha uma opção: ");
                OpçaoDeCla = VereficaQuantia(NovoCla, (_NumeroDoCla));
                if (OpçaoDeCla == NovoCla)
                {
                    _ConjuntoDeClas = (tCla *)realloc(_ConjuntoDeClas, (_NumeroDoCla + 1) * sizeof(tCla));
                    _ConjuntoDeClas[_NumeroDoCla] = CriarCla();
                    _NumeroDoCla++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCla Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                    return _NumeroDoCla;
                }
                else
                {
                    return OpçaoDeCla;
                }
            }
        }
        break;
    }

    case 3:
        if (_NumeroDoJutsu == 0)
        {
            printf("\nNao a Nenhum Jutsu Cadastrado no Momento!\nDeseja Cadastrar Um Novo Jutsu?(s/n) ");
            if (simOUnao() == 1)
            {
                _ConjuntoDeJutsus = (tJutsu *)malloc(1 * sizeof(tJutsu));
                _ConjuntoDeJutsus[_NumeroDoJutsu] = CriarJutsu();
                _NumeroDoJutsu++;
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsu Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                congelar_tela();
            }
            else
            {
                return -1;
            }
        }
        else
        {
            if (TipoDeCaso == 1)
            {
                printf("Deseja Adicionar Mais Algum Jutsu?(s/n) ");
                if (simOUnao() == 1)
                {
                    _ConjuntoDeJutsus = (tJutsu *)realloc(_ConjuntoDeJutsus, (_NumeroDoJutsu + 1) * sizeof(tJutsu));
                    _ConjuntoDeJutsus[_NumeroDoJutsu] = CriarJutsu();
                    _NumeroDoJutsu++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsu Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                }
            }
            else if (TipoDeCaso == 2)
            {
                int OpçaoDeJutsu;
                int NovoJutsu = 0;
                printf("\n{ 0 } - Cadastrar Novo Jutsu\nEscolha uma opção: ");
                OpçaoDeJutsu = VereficaQuantia(NovoJutsu, _NumeroDoJutsu);
                if (OpçaoDeJutsu == NovoJutsu)
                {
                    _ConjuntoDeJutsus = (tJutsu *)realloc(_ConjuntoDeJutsus, (_NumeroDoJutsu + 1) * sizeof(tJutsu));
                    _ConjuntoDeJutsus[_NumeroDoJutsu] = CriarJutsu();
                    _NumeroDoJutsu++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsu Cadastrado Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                    return _NumeroDoJutsu;
                }
                else
                {
                    return OpçaoDeJutsu;
                }
            }
        }
        break;

    case 4:
        if (_NumeroDaMissao == 0)
        {
            printf("\nNao a Nenhuma Missao Cadastrada no Momento!\nDeseja Cadastrar Uma Nova Missao?(s/n) ");
            if (simOUnao() == 1)
            {
                _ConjuntoDeMissoes = (tMissao *)malloc(1 * sizeof(tMissao));
                _ConjuntoDeMissoes[_NumeroDaMissao] = CriarMissao();
                _NumeroDaMissao++;
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nMissao Cadastrada Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                congelar_tela();
            }
            else
            {
                return -1;
            }
        }

        else
        {
            if (TipoDeCaso == 1)
            {
                printf("Deseja Adicionar Mais Alguma Missao?(s/n) ");
                if (simOUnao() == 1)
                {
                    _ConjuntoDeMissoes = (tMissao *)realloc(_ConjuntoDeMissoes, (_NumeroDaMissao + 1) * sizeof(tMissao));
                    _ConjuntoDeMissoes[_NumeroDaMissao] = CriarMissao();
                    _NumeroDaMissao++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nMissao Cadastrada Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                }
            }
            else if (TipoDeCaso == 2)
            {
                int OpçaoDeMissao;
                int NovaMissao = 0;
                printf("\n{ 0 } - Cadastrar Nova Missao\nEscolha uma opção: ");
                OpçaoDeMissao = VereficaQuantia(NovaMissao, _NumeroDaMissao);
                if (OpçaoDeMissao == NovaMissao)
                {
                    _ConjuntoDeMissoes = (tMissao *)realloc(_ConjuntoDeMissoes, (_NumeroDaMissao + 1) * sizeof(tMissao));
                    _ConjuntoDeMissoes[_NumeroDaMissao] = CriarMissao();
                    _NumeroDaMissao++;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\nMissao Cadastrada Com Sucesso!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
                    congelar_tela();
                    return _NumeroDaMissao;
                }
                else
                {
                    return OpçaoDeMissao;
                }
            }
        }
        break;
    }
    return 0;
}

tNinja CriarShinobi()
{
    tNinja CadastroDeShinobi;
    int i, j, OpçaoDeJutsu, OpçaoDeCla, NumDeJutsus;
    char StrAux[100];

    //=======================================================================================================================================================================================//

    while (1)
    {
        int erro = 0;
        limpar_tela();
        printf("\n<~==[ CADASTRO DE SHINOBI: NOME DO SHINOBI ]==~>\n\nDigite o Nome Do Shinobi: ");
        VerificadorDeTexto(StrAux);
        if (_NumeroDoShinobi > 0)
        {
            for (i = 0; i < _NumeroDoShinobi; i++)
            {
                if (strcmp(StrAux, _ConjuntoDeShinobis[i].Nome) == 0)
                {
                    erro = 1;
                    limpar_tela();
                    printf("\n<~==[ CADASTRO DE SHINOBI: NOME DO SHINOBI ]==~>\n\nErro! Um Shinobi Com Este Nome Ja Foi Cadastrado!");
                    congelar_tela();
                    break;
                }
            }
        }

        if (erro)
        {
            continue;
        }
        else
        {
            break;
        }
    }
    CadastroDeShinobi.Nome = (char *)malloc(((strlen(StrAux)) + 1) * sizeof(char));
    strcpy(CadastroDeShinobi.Nome, StrAux);

    //=======================================================================================================================================================================================//

    CadastroDeShinobi.CaracteristicasDoCla = malloc(sizeof(tCla));
    limpar_tela();
    printf("\n<~==[ CADASTRO DE SHINOBI: CLA DO SHINOBI ]==~>\n\nSelecione o Cla Desejado...");
    congelar_tela();
    OpçaoDeCla = EscolheOpçao(2);

    if (OpçaoDeCla != 0)
    {
        CadastroDeShinobi.CaracteristicasDoCla = malloc(sizeof(tCla));

        if (_ConjuntoDeClas[OpçaoDeCla - 1].NomeDoCla != NULL)
        {
            CadastroDeShinobi.CaracteristicasDoCla->NomeDoCla = (char *)malloc((strlen(_ConjuntoDeClas[OpçaoDeCla - 1].NomeDoCla) + 1) * sizeof(char));
            strcpy(CadastroDeShinobi.CaracteristicasDoCla->NomeDoCla, _ConjuntoDeClas[OpçaoDeCla - 1].NomeDoCla);
        }

        if (_ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusHereditarios > 0 && _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios != NULL)
        {
            CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios = malloc(((_ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusHereditarios)) * sizeof(tJutsu *));
            CadastroDeShinobi.CaracteristicasDoCla->QuantidadeDeJutsusHereditarios = _ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusHereditarios;
            for (i = 0; i < _ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusHereditarios; i++)
            {
                CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->Nome != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->Nome = (char *)malloc(((strlen(_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->Nome)) + 1) * sizeof(char));
                    strcpy(CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->Nome, _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->Nome);
                }

                if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->TipoJutsu != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->TipoJutsu = (char *)malloc(((strlen(_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->TipoJutsu)) + 1) * sizeof(char));
                    strcpy(CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->TipoJutsu, _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->TipoJutsu);
                }

                if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->QuantidadeDeElementos > 0 && _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->Elementos != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->QuantidadeDeElementos = _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->QuantidadeDeElementos;
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->Elementos = (char **)malloc(((_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->QuantidadeDeElementos) + 1) * sizeof(char *));
                    for (j = 0; j < (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->QuantidadeDeElementos); j++)
                    {
                        if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->Elementos[j])
                        {
                            CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->Elementos[j] = (char *)malloc((strlen(_ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->Elementos[j]) + 1) * sizeof(char));
                            strcpy(CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->Elementos[j], _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->Elementos[j]);
                        }
                    }
                }
                else
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->QuantidadeDeElementos = 0;
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->Elementos = NULL;
                }

                CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->CustodeChakra = _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->CustodeChakra;

                CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios[i]->PoderdeAtaque = _ConjuntoDeClas[OpçaoDeCla - 1].JutsusHereditarios[i]->PoderdeAtaque;
            }
        }
        else
        {
            CadastroDeShinobi.CaracteristicasDoCla->QuantidadeDeJutsusHereditarios = 0;
            CadastroDeShinobi.CaracteristicasDoCla->JutsusHereditarios = NULL;
        }

        if (_ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusTradicionais > 0 && _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais != NULL)
        {
            CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais = malloc(((_ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusTradicionais)) * sizeof(tJutsu *));
            CadastroDeShinobi.CaracteristicasDoCla->QuantidadeDeJutsusTradicionais = _ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusTradicionais;
            for (i = 0; i < _ConjuntoDeClas[OpçaoDeCla - 1].QuantidadeDeJutsusTradicionais; i++)
            {
                CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->Nome != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->Nome = (char *)malloc((strlen(_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->Nome) + 1) * sizeof(char));
                    strcpy(CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->Nome, _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->Nome);
                }

                if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->TipoJutsu != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->TipoJutsu) + 1) * sizeof(char));
                    strcpy(CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->TipoJutsu, _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->TipoJutsu);
                }

                if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->QuantidadeDeElementos > 0 && _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->Elementos != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->QuantidadeDeElementos = _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->QuantidadeDeElementos;
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->Elementos = (char **)malloc(((_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->QuantidadeDeElementos) + 1) * sizeof(char *));
                    for (j = 0; j < (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->QuantidadeDeElementos); j++)
                    {
                        if (_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->Elementos[j] != NULL)
                        {
                            CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->Elementos[j] = (char *)malloc((strlen(_ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->Elementos[j]) + 1) * sizeof(char));
                            strcpy(CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->Elementos[j], _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->Elementos[j]);
                        }
                    }
                }
                else
                {
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->QuantidadeDeElementos = 0;
                    CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->Elementos = NULL;
                }

                CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->CustodeChakra = _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->CustodeChakra;

                CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais[i]->PoderdeAtaque = _ConjuntoDeClas[OpçaoDeCla - 1].JutsusTradicionais[i]->PoderdeAtaque;
            }
        }
        else
        {
            CadastroDeShinobi.CaracteristicasDoCla->QuantidadeDeJutsusTradicionais = 0;
            CadastroDeShinobi.CaracteristicasDoCla->JutsusTradicionais = NULL;
        }
    }

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE SHINOBI: PATENTE DO SHINOBI ]==~>\n\n{ 1 } Estudante da Academia - em formação\n{ 2 } Genin - graduado iniciante\n{ 3 } Chunin - ninja de nível médio\n{ 4 } Tokubetsu Jounin - especialista em área específica\n{ 5 } Jounin - elite da vila\n{ 6 } ANBU - força especial secreta\n{ 7 } Kage - lider da vila\n\nEscolha uma opção: ");
    SelecionaPatente((VereficaQuantia(1, 7)), StrAux);
    CadastroDeShinobi.Patente = (char *)malloc(((strlen(StrAux)) + 1) * sizeof(char));
    strcpy(CadastroDeShinobi.Patente, StrAux);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE SHINOBI: RESERVA DE CHAKRA DO SHINOBI ]==~>\n\nDigite a Reserva de Chakra do Shinobi(0/100): ");
    CadastroDeShinobi.QuantidadeDeChakra = VereficaQuantia(1, 100);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE SHINOBI: ELEMENTO PRINCIPAL DO SHINOBI ]==~>\n\nDigite o Elemento Principal do Shinobi: ");
    VerificadorDeTexto(StrAux);
    CadastroDeShinobi.ElementoPrincipal = (char *)malloc(((strlen(StrAux)) + 1) * sizeof(char));
    strcpy(CadastroDeShinobi.ElementoPrincipal, StrAux);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE SHINOBI: JUTSUS DO SHINOBI ]==~>\n\nO Shinobi Domina Algum Jutsu?(s/n) ");
    if (simOUnao() == 1)
    {
        int *aux = calloc(101, sizeof(int));

        limpar_tela();
        printf("\n<~==[ CADASTRO DE SHINOBI: JUTSUS DO SHINOBI ]==~>\n\nDigite Quantos Jutsus o Shinobi Possui: ");
        NumDeJutsus = VereficaQuantia(1, 1000);

        CadastroDeShinobi.QuantidadeDeJutsus = NumDeJutsus;
        CadastroDeShinobi.CaracteristicasDoJutsu = malloc(NumDeJutsus * sizeof(tJutsu *));
        for (i = 0; i < NumDeJutsus; i++)
        {
            CadastroDeShinobi.CaracteristicasDoJutsu[i] = malloc(sizeof(tJutsu));

            limpar_tela();
            printf("\n<~==[ CADASTRO DE SHINOBI: JUTSUS DO SHINOBI ]==~>\n\nSelecione o Jutsu Desejado...");
            congelar_tela();

            OpçaoDeJutsu = EscolheOpçao(3);
            aux[i] = OpçaoDeJutsu;

            if (i > 0 && (VerficaRepetiçaoDeOpçao(i, NumDeJutsus, aux, OpçaoDeJutsu)))
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPÇAO: SELECIONE -> JUTSU DESEJADO ]==~>\n\nErro! Esta Opçao Ja Foi Escolhida!");
                i--;
                congelar_tela();
                continue;
            }

            if (OpçaoDeJutsu != 0)
            {
                CadastroDeShinobi.CaracteristicasDoJutsu[i] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoJutsu[i]->Nome = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome) + 1) * sizeof(char));
                    strcpy(CadastroDeShinobi.CaracteristicasDoJutsu[i]->Nome, _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome);
                }

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoJutsu[i]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu) + 1) * sizeof(char));
                    strcpy(CadastroDeShinobi.CaracteristicasDoJutsu[i]->TipoJutsu, _ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu);
                }

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos > 0 && _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos != NULL)
                {
                    CadastroDeShinobi.CaracteristicasDoJutsu[i]->QuantidadeDeElementos = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos;
                    CadastroDeShinobi.CaracteristicasDoJutsu[i]->Elementos = (char **)malloc(((_ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos) + 1) * sizeof(char *));
                    for (j = 0; j < _ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos; j++)
                    {
                        if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j] != NULL)
                        {
                            CadastroDeShinobi.CaracteristicasDoJutsu[i]->Elementos[j] = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j]) + 1) * sizeof(char));
                            strcpy(CadastroDeShinobi.CaracteristicasDoJutsu[i]->Elementos[j], _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j]);
                        }
                    }
                }
                else
                {
                    CadastroDeShinobi.CaracteristicasDoJutsu[i]->QuantidadeDeElementos = 0;
                    CadastroDeShinobi.CaracteristicasDoJutsu[i]->Elementos = NULL;
                }

                CadastroDeShinobi.CaracteristicasDoJutsu[i]->CustodeChakra = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].CustodeChakra;

                CadastroDeShinobi.CaracteristicasDoJutsu[i]->PoderdeAtaque = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].PoderdeAtaque;
            }
        }
    }
    else
    {
        CadastroDeShinobi.QuantidadeDeJutsus = 0;
        CadastroDeShinobi.CaracteristicasDoJutsu = NULL;
    }

    //=======================================================================================================================================================================================//

    return CadastroDeShinobi;
}

tCla CriarCla()
{
    tCla CadastroDeCla;
    char StrAux[100];
    int i, j, OpçaoDeJutsu = 0;

    while (1)
    {
        int erro = 0;
        limpar_tela();
        printf("\n<~==[ CADASTRO DE CLA: NOME DO CLA ]==~>\n\nDigite o Nome do Cla: ");
        VerificadorDeTexto(StrAux);
        if (_NumeroDoCla > 0)
        {
            for (i = 0; i < _NumeroDoCla; i++)
            {
                if (strcmp(StrAux, _ConjuntoDeClas[i].NomeDoCla) == 0)
                {
                    erro = 1;
                    limpar_tela();
                    printf("\n<~==[ CADASTRO DE CLA: NOME DO CLA ]==~>\n\nErro! Um Cla Com Este Nome Ja Foi Cadastrado!");
                    congelar_tela();
                    break;
                }
            }
        }

        if (erro)
        {
            continue;
        }
        else
        {
            break;
        }
    }
    CadastroDeCla.NomeDoCla = (char *)malloc((strlen(StrAux) + 1) * sizeof(char));
    strcpy(CadastroDeCla.NomeDoCla, StrAux);
    fflush(stdin);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE CLA: JUTSU HEREDITARIO DO CLA ]==~>\n\nO Cla Possui Algum Jutsu Hereditario?(s/n) ");
    if (simOUnao() == 1)
    {
        int i, NumDeJutsusHereditarios;
        int *aux = calloc(101, sizeof(int));

        limpar_tela();
        printf("\n<~==[ CADASTRO DE CLA: JUTSU HEREDITARIO DO CLA ]==~>\n\nDigite Quantos Jutsus Hereditarios o Cla Possui: ");
        NumDeJutsusHereditarios = VereficaQuantia(1, 100);

        CadastroDeCla.QuantidadeDeJutsusHereditarios = NumDeJutsusHereditarios;
        CadastroDeCla.JutsusHereditarios = malloc(NumDeJutsusHereditarios * sizeof(tJutsu *));
        for (i = 0; i < NumDeJutsusHereditarios; i++)
        {
            CadastroDeCla.JutsusHereditarios[i] = malloc(sizeof(tJutsu));

            limpar_tela();
            printf("\n<~==[ CADASTRO DE CLA: JUTSU HEREDITARIO DO CLA ]==~>\n\nSelecione o Jutsu Desejado...");
            congelar_tela();

            OpçaoDeJutsu = EscolheOpçao(3);
            aux[i] = OpçaoDeJutsu;

            if (i > 0 && (VerficaRepetiçaoDeOpçao(i, NumDeJutsusHereditarios, aux, OpçaoDeJutsu)))
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPÇAO: SELECIONE -> JUTSU DESEJADO ]==~>\n\nErro! Esta Opçao Ja Foi Escolhida!");
                i--;
                congelar_tela();
                continue;
            }

            if (OpçaoDeJutsu != 0)
            {
                CadastroDeCla.JutsusHereditarios[i] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome != NULL)
                {
                    CadastroDeCla.JutsusHereditarios[i]->Nome = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome) + 1) * sizeof(char));
                    strcpy(CadastroDeCla.JutsusHereditarios[i]->Nome, _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome);
                }

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu != NULL)
                {
                    CadastroDeCla.JutsusHereditarios[i]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu) + 1) * sizeof(char));
                    strcpy(CadastroDeCla.JutsusHereditarios[i]->TipoJutsu, _ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu);
                }

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos > 0 && _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos != NULL)
                {
                    CadastroDeCla.JutsusHereditarios[i]->QuantidadeDeElementos = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos;
                    CadastroDeCla.JutsusHereditarios[i]->Elementos = (char **)malloc(((_ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos)) * sizeof(char *));
                    for (j = 0; j < _ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos; j++)
                    {
                        if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j] != NULL)
                        {
                            CadastroDeCla.JutsusHereditarios[i]->Elementos[j] = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j]) + 1) * sizeof(char));
                            strcpy(CadastroDeCla.JutsusHereditarios[i]->Elementos[j], _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j]);
                        }
                    }
                }
                else
                {
                    CadastroDeCla.JutsusHereditarios[i]->QuantidadeDeElementos = 0;
                    CadastroDeCla.JutsusHereditarios[i]->Elementos = NULL;
                }

                CadastroDeCla.JutsusHereditarios[i]->CustodeChakra = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].CustodeChakra;

                CadastroDeCla.JutsusHereditarios[i]->PoderdeAtaque = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].PoderdeAtaque;
            }
        }
    }
    else
    {
        CadastroDeCla.QuantidadeDeJutsusHereditarios = 0;
        CadastroDeCla.JutsusHereditarios = NULL;
    }

    //=======================================================================================================================================================================================//

    OpçaoDeJutsu = 0;

    limpar_tela();
    printf("\n<~==[ CADASTRO DE CLA: JUTSU TRADICIONAL DO CLA ]==~>\n\nO Cla Possui Algum Jutsu Tradicional?(s/n) ");
    if (simOUnao() == 1)
    {
        int i, NumDeJutsusTradicionais;
        int *aux = calloc(101, sizeof(int));

        limpar_tela();
        printf("\n<~==[ CADASTRO DE CLA: JUTSU TRADICIONAL DO CLA ]==~>\n\nDigite Quantos Jutsus Tradicionais o Cla Possui: ");
        NumDeJutsusTradicionais = VereficaQuantia(1, 100);

        CadastroDeCla.QuantidadeDeJutsusTradicionais = NumDeJutsusTradicionais;
        CadastroDeCla.JutsusTradicionais = malloc(NumDeJutsusTradicionais * sizeof(tJutsu *));
        for (i = 0; i < NumDeJutsusTradicionais; i++)
        {
            CadastroDeCla.JutsusTradicionais[i] = malloc(sizeof(tJutsu));

            limpar_tela();
            printf("\n<~==[ CADASTRO DE CLA: JUTSU TRADICIONAL CLA ]==~>\n\nSelecione o Jutsu Desejado...");
            congelar_tela();

            OpçaoDeJutsu = EscolheOpçao(3);
            aux[i] = OpçaoDeJutsu;

            if (i > 0 && (VerficaRepetiçaoDeOpçao(i, NumDeJutsusTradicionais, aux, OpçaoDeJutsu)))
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> JUTSU DESEJADO ]==~>\n\nErro! Esta Opçao Ja Foi Escolhida!");
                i--;
                congelar_tela();
                continue;
            }

            if (OpçaoDeJutsu != 0)
            {
                CadastroDeCla.JutsusTradicionais[i] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome != NULL)
                {
                    CadastroDeCla.JutsusTradicionais[i]->Nome = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome) + 1) * sizeof(char));
                    strcpy(CadastroDeCla.JutsusTradicionais[i]->Nome, _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Nome);
                }

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu != NULL)
                {
                    CadastroDeCla.JutsusTradicionais[i]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu) + 1) * sizeof(char));
                    strcpy(CadastroDeCla.JutsusTradicionais[i]->TipoJutsu, _ConjuntoDeJutsus[OpçaoDeJutsu - 1].TipoJutsu);
                }

                if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos > 0 && _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos != NULL)
                {
                    CadastroDeCla.JutsusTradicionais[i]->QuantidadeDeElementos = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos;
                    CadastroDeCla.JutsusTradicionais[i]->Elementos = (char **)malloc(((_ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos) + 1) * sizeof(char *));
                    for (j = 0; j < _ConjuntoDeJutsus[OpçaoDeJutsu - 1].QuantidadeDeElementos; j++)
                    {
                        if (_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j] != NULL)
                        {
                            CadastroDeCla.JutsusTradicionais[i]->Elementos[j] = (char *)malloc((strlen(_ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j]) + 1) * sizeof(char));
                            strcpy(CadastroDeCla.JutsusTradicionais[i]->Elementos[j], _ConjuntoDeJutsus[OpçaoDeJutsu - 1].Elementos[j]);
                        }
                    }
                }
                else
                {
                    CadastroDeCla.JutsusTradicionais[i]->QuantidadeDeElementos = 0;
                    CadastroDeCla.JutsusTradicionais[i]->Elementos = NULL;
                }

                CadastroDeCla.JutsusTradicionais[i]->CustodeChakra = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].CustodeChakra;

                CadastroDeCla.JutsusTradicionais[i]->PoderdeAtaque = _ConjuntoDeJutsus[OpçaoDeJutsu - 1].PoderdeAtaque;
            }
        }
    }
    else
    {
        CadastroDeCla.QuantidadeDeJutsusTradicionais = 0;
        CadastroDeCla.JutsusTradicionais = NULL;
    }

    //=======================================================================================================================================================================================//

    return CadastroDeCla;
}

tJutsu CriarJutsu()
{
    tJutsu CadastroDeJutsu;
    char StrAuX[100];
    int i;

    while (1)
    {
        int erro = 0;
        limpar_tela();
        printf("\n<~==[ CADASTRO DE JUTSU: NOME DO JUTSU ]==~>\n\nDigite o Nome do Jutsu: ");
        VerificadorDeTexto(StrAuX);
        if (_NumeroDoJutsu > 0)
        {
            for (i = 0; i < _NumeroDoJutsu; i++)
            {
                if (strcmp(StrAuX, _ConjuntoDeJutsus[i].Nome) == 0)
                {
                    erro = 1;
                    limpar_tela();
                    printf("\n<~==[ CADASTRO DE JUTSU: NOME DO JUTSU ]==~>\n\nErro! Um Jutsu Com Este Nome Ja Foi Cadastrado!");
                    congelar_tela();
                    break;
                }
            }
        }

        if (erro)
        {
            continue;
        }
        else
        {
            break;
        }
    }
    CadastroDeJutsu.Nome = (char *)malloc((strlen(StrAuX) + 1) * sizeof(char));
    strcpy(CadastroDeJutsu.Nome, StrAuX);
    fflush(stdin);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE JUTSU: TIPO DE JUTSU ]==~>\n\nDigite o Tipo do Jutsu: ");
    VerificadorDeTexto(StrAuX);
    CadastroDeJutsu.TipoJutsu = (char *)malloc((strlen(StrAuX) + 1) * sizeof(char));
    strcpy(CadastroDeJutsu.TipoJutsu, StrAuX);
    fflush(stdin);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE JUTSU: ELEMENTOS DO JUTSU ]==~>\n\nO Jutsu Tem Relaçao Com Algum Elemento?(s/n) ");
    if (simOUnao() == 1)
    {
        int QuantidadeDeElementos;

        limpar_tela();
        printf("\n<~==[ CADASTRO DE JUTSU: ELEMENTOS DO JUTSU ]==~>\n\nDigite Quantos Elementos o jutsu faz relaçao: ");
        QuantidadeDeElementos = VereficaQuantia(1, 20);
        CadastroDeJutsu.QuantidadeDeElementos = QuantidadeDeElementos;
        CadastroDeJutsu.Elementos = (char **)malloc((QuantidadeDeElementos + 1) * sizeof(char *));
        for (i = 0; i < QuantidadeDeElementos; i++)
        {
            limpar_tela();
            printf("\n<~==[ CADASTRO DE JUTSU: ELEMENTO %d DO JUTSU ]==~>\n\nDigite o Elemento %d: ", i + 1, i + 1);
            VerificadorDeTexto(StrAuX);
            CadastroDeJutsu.Elementos[i] = (char *)malloc((strlen(StrAuX) + 1) * sizeof(char));
            strcpy(CadastroDeJutsu.Elementos[i], StrAuX);
            fflush(stdin);
        }
    }
    else
    {
        CadastroDeJutsu.QuantidadeDeElementos = 0;
        CadastroDeJutsu.Elementos = NULL;
    }

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE JUTSU: CUSTO DE CHAKRA DO JUTSU ]==~>\n\nDigite o Custo de Chakra do Jutsu(0/100): ");
    CadastroDeJutsu.CustodeChakra = VereficaQuantia(1, 100);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE JUTSU: PODER DE ATAQUE DO JUTSU ]==~>\n\nDigite o Poder de Ataque do Jutsu(0/100): ");
    CadastroDeJutsu.PoderdeAtaque = VereficaQuantia(1, 100);

    //=======================================================================================================================================================================================//

    return CadastroDeJutsu;
}

tMissao CriarMissao()
{
    tMissao CadastroDeMissao;
    char StrAuX[100];
    int i;

    //=======================================================================================================================================================================================//

    while (1)
    {
        int erro = 0;
        limpar_tela();
        printf("\n<~==[ CADASTRO DE MISSAO: TITULO DA MISSAO ]==~>\n\nDigite o Titulo da Missao: ");
        VerificadorDeTexto(StrAuX);
        if (_NumeroDaMissao > 0)
        {
            for (i = 0; i < _NumeroDaMissao; i++)
            {
                if (strcmp(StrAuX, _ConjuntoDeMissoes[i].Titulo) == 0)
                {
                    erro = 1;
                    break;
                }
            }
        }

        if (erro)
        {
            limpar_tela();
            printf("\n<~==[ CADASTRO DE MISSAO: TITULO DA MISSAO ]==~>\n\nErro! Uma Missao Com Este Titulo Ja Foi Cadastrada!");
            congelar_tela();
            continue;
        }
        else
        {
            break;
        }
    }
    CadastroDeMissao.Titulo = (char *)malloc((strlen(StrAuX) + 1) * sizeof(char));
    strcpy(CadastroDeMissao.Titulo, StrAuX);
    fflush(stdin);

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE MISSAO: NIVEL DA MISSAO ]==~>\n\nDigite o Nivel de Dificuldade da Missao(D/C/B/A/S): ");
    VerificaNivelDeDificuldade(StrAuX);
    CadastroDeMissao.NivelDificuldade = (char *)malloc((strlen(StrAuX) + 1) * sizeof(char));
    strcpy(CadastroDeMissao.NivelDificuldade, StrAuX);
    fflush(stdin);

    //=======================================================================================================================================================================================//

    int *aux = calloc(101, sizeof(int));
    int OpçaoDeShinobi = 0;

    limpar_tela();
    printf("\n<~==[ CADASTRO DE MISSAO: LIDER DA MISSAO ]==~>\n\nSelecione o Lider Desejado...");
    congelar_tela();

    while (1)
    {
        OpçaoDeShinobi = EscolheOpçao(1);
        aux[0] = OpçaoDeShinobi;

        if (strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Estudante da Academia") != 0 && strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Genin") != 0)
        {
            if (strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Chunin") == 0 && (strcmp(CadastroDeMissao.NivelDificuldade, "A") == 0 || strcmp(CadastroDeMissao.NivelDificuldade, "S") == 0))
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO DE MISSAO: LIDER DA MISSAO ]==~>\n\nErro! Um Chunin só pode liderar missões de nível C ou B!");
                congelar_tela();
                continue;
            }
        }
        else
        {
            limpar_tela();
            if ((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Estudante da Academia") == 0))
            {
                printf("\n<~==[ CADASTRO DE MISSAO: LIDER DA MISSAO ]==~>\n\nErro! Um Estudante Da Academia Ainda Não Está Pronto Para Liderar Missões!");
                congelar_tela();
            }
            else if ((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Genin") == 0))
            {
                printf("\n<~==[ CADASTRO DE MISSAO: LIDER DA MISSAO ]==~>\n\nErro! Um Genin não possui autoridade para liderar missões!");
                congelar_tela();
            }
            continue;
        }
        break;
    }

    if (OpçaoDeShinobi != 0)
    {
        CadastroDeMissao.LiderResponsavel = malloc(sizeof(tNinja));

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Nome != NULL)
        {
            CadastroDeMissao.LiderResponsavel->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Nome) + 1) * sizeof(char));
            strcpy(CadastroDeMissao.LiderResponsavel->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].Nome);
        }

        CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla = malloc(sizeof(tCla));

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->NomeDoCla != NULL)
        {
            CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->NomeDoCla = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->NomeDoCla) + 1) * sizeof(char));
            strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->NomeDoCla, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->NomeDoCla);
        }

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios != NULL)
        {
            int j;
            CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->QuantidadeDeJutsusHereditarios = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios;
            CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios = (tJutsu **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios) + 1) * sizeof(tJutsu *));
            for (j = 0; j < (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios); j++)
            {
                CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Nome != NULL)
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Nome) + 1) * sizeof(char));
                    strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Nome);
                }

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu != NULL)
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu) + 1) * sizeof(char));
                    strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu);
                }

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos != NULL)
                {
                    int k;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos = (char **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos) + 1) * sizeof(char *));
                    for (k = 0; k < _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos; k++)
                    {
                        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k] != NULL)
                        {
                            CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k] = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k]) + 1) * sizeof(char));
                            strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k], _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k]);
                        }
                    }
                }
                else
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos = 0;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos = NULL;
                }

                CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->CustodeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->CustodeChakra;

                CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusHereditarios[j]->PoderdeAtaque = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->PoderdeAtaque;
            }
        }

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais != NULL)
        {
            int j;
            CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->QuantidadeDeJutsusTradicionais = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais;
            CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais = (tJutsu **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais) + 1) * sizeof(tJutsu *));
            for (j = 0; j < (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais); j++)
            {
                CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Nome != NULL)
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Nome) + 1) * sizeof(char));
                    strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Nome);
                }

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu != NULL)
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu) + 1) * sizeof(char));
                    strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu);
                }

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos != NULL)
                {
                    int k;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos = (char **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos) + 1) * sizeof(char *));
                    for (k = 0; k < _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos; k++)
                    {
                        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k] != NULL)
                        {
                            CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k] = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k]) + 1) * sizeof(char));
                            strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k], _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k]);
                        }
                    }
                }
                else
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos = 0;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos = NULL;
                }

                CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->CustodeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->CustodeChakra;

                CadastroDeMissao.LiderResponsavel->CaracteristicasDoCla->JutsusTradicionais[j]->PoderdeAtaque = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->PoderdeAtaque;
            }
        }

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente != NULL)
        {
            CadastroDeMissao.LiderResponsavel->Patente = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente) + 1) * sizeof(char));
            strcpy(CadastroDeMissao.LiderResponsavel->Patente, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente);
        }

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeChakra)
        {
            CadastroDeMissao.LiderResponsavel->QuantidadeDeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeChakra;
        }

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].ElementoPrincipal != NULL)
        {
            CadastroDeMissao.LiderResponsavel->ElementoPrincipal = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].ElementoPrincipal) + 1) * sizeof(char));
            strcpy(CadastroDeMissao.LiderResponsavel->ElementoPrincipal, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].ElementoPrincipal);
        }

        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu != NULL)
        {
            int j;
            CadastroDeMissao.LiderResponsavel->QuantidadeDeJutsus = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus;
            CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu = (tJutsu **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus) + 1) * sizeof(tJutsu *));
            for (j = 0; j < (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus); j++)
            {
                CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j] = malloc(sizeof(tJutsu));

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Nome)
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Nome) + 1) * sizeof(char));
                    strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Nome);
                }

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->TipoJutsu != NULL)
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->TipoJutsu) + 1) * sizeof(char));
                    strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->TipoJutsu, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->TipoJutsu);
                }

                if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos != NULL)
                {
                    int k;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->QuantidadeDeElementos = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->Elementos = (char **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos) + 1) * sizeof(char *));
                    for (k = 0; k < _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos; k++)
                    {
                        if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos[k])
                        {
                            CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->Elementos[k] = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos[k]) + 1) * sizeof(char));
                            strcpy(CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->Elementos[k], _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos[k]);
                        }
                    }
                }
                else
                {
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->QuantidadeDeElementos = 0;
                    CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->Elementos = NULL;
                }

                CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->CustodeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->CustodeChakra;

                CadastroDeMissao.LiderResponsavel->CaracteristicasDoJutsu[j]->PoderdeAtaque = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->PoderdeAtaque;
            }
        }
    }

    //=======================================================================================================================================================================================//

    limpar_tela();
    printf("\n<~==[ CADASTRO DE MISSAO: STATUS ]==~>\n\n");
    printf("{ 1 } Pendente\n{ 2 } Em Andamento\n{ 3 } Concluida\n{ 4 } Falha\n{ 5 } Cancelada\n\nEscolha uma opção: ");
    SelecionaStatus(VereficaQuantia(1, 5), StrAuX);
    CadastroDeMissao.Status = (char *)malloc(((strlen(StrAuX)) + 1) * sizeof(char));
    strcpy(CadastroDeMissao.Status, StrAuX);

    //=======================================================================================================================================================================================//

    int QuantiaAux;
    limpar_tela();
    printf("\n<~==[ CADASTRO DE MISSAO: QUANTIDADE DE NINJAS ]==~>\n");
    if (strcmp(CadastroDeMissao.NivelDificuldade, "D") == 0)
    {
        printf("\nDigite Quantos Ninjas De Acordo com o Nivel D (1 a 2): ");
        QuantiaAux = VereficaQuantia(1, 2);
    }
    else if (strcmp(CadastroDeMissao.NivelDificuldade, "C") == 0)
    {
        printf("\nDigite Quantos Ninjas De Acordo com o Nivel C (1 a 3): ");
        QuantiaAux = VereficaQuantia(1, 3);
    }
    else if (strcmp(CadastroDeMissao.NivelDificuldade, "B") == 0)
    {
        printf("\nDigite Quantos Ninjas De Acordo com o Nivel B (1 a 4): ");
        QuantiaAux = VereficaQuantia(1, 4);
    }
    else if (strcmp(CadastroDeMissao.NivelDificuldade, "A") == 0)
    {
        printf("\nDigite Quantos Ninjas De Acordo com o Nivel A (1 a 5): ");
        QuantiaAux = VereficaQuantia(1, 5);
    }
    else if (strcmp(CadastroDeMissao.NivelDificuldade, "S") == 0)
    {
        printf("\nDigite Quantos Ninjas De Acordo com o Nivel S (1 a 6): ");
        QuantiaAux = VereficaQuantia(1, 6);
    }
    CadastroDeMissao.QuantidadeDeNinjas = QuantiaAux;

    //=======================================================================================================================================================================================//

    CadastroDeMissao.NinjasDaMissao = malloc(QuantiaAux * sizeof(tNinja *));
    for (i = 0; i < QuantiaAux; i++)
    {
        CadastroDeMissao.NinjasDaMissao[i] = malloc(sizeof(tNinja));
        limpar_tela();

        OpçaoDeShinobi = EscolheOpçao(1);
        aux[i + 1] = OpçaoDeShinobi;

        if (strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Estudante da Academia") != 0)
        {
            if ((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Genin") == 0) && (((strcmp(CadastroDeMissao.NivelDificuldade, "D") != 0) && (strcmp(CadastroDeMissao.NivelDificuldade, "C") != 0))) && ((strcmp(CadastroDeMissao.LiderResponsavel->Patente, "Jounin") != 0) && ((strcmp(CadastroDeMissao.LiderResponsavel->Patente, "Chunin") != 0))))
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Os Genins só Podem participar de missões D ou C, e deve estar acompanhados por um Chunin ou um Jounin!");
                congelar_tela();
                i--;
                continue;
            }
            else if ((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Chunin") == 0) && strcmp(CadastroDeMissao.NivelDificuldade, "S") == 0)
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Os Chunins Não Podem Participar de Missões Rank S Sem autorização do Hokage!");
                congelar_tela();
                i--;
                continue;
            }
            else if (((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Jounin") == 0) || (strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Tokubetsu Jounin") == 0)))
            {
                if ((strcmp(CadastroDeMissao.LiderResponsavel->Patente, "Chunin") == 0))
                {
                    limpar_tela();
                    printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Um Jounin Não Segue Ordens De Um Chunin. Isso Fere A Hierarquia Ninja!");
                    congelar_tela();
                    i--;
                    continue;
                }
                else if (strcmp(CadastroDeMissao.NivelDificuldade, "S") == 0)
                {
                    limpar_tela();
                    printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Os Tokubetsu Jounins e Jounins só Atuam em Missoes de Rank S Com Ordem do Hokage!");
                    congelar_tela();
                    i--;
                    continue;
                }
            }
            else if (((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "ANBU") == 0)) && (strcmp(CadastroDeMissao.LiderResponsavel->Patente, "Kage") != 0 && strcmp(CadastroDeMissao.LiderResponsavel->Patente, "Kage") != 0) && strcmp(CadastroDeMissao.NivelDificuldade, "A") != 0 && strcmp(CadastroDeMissao.NivelDificuldade, "S") != 0)
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Os ANBU só atuam em missões Rank A e S, sob ordens diretas do Hokage!");
                congelar_tela();
                i--;
                continue;
            }
            else if ((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente, "Kage") == 0))
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Os Kages Não Seguem Ordens e Podem Liderar Qualquer Missão!");
                congelar_tela();
                i--;
                continue;
            }
        }
        else
        {
            limpar_tela();
            printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Um Estudante Ainda Não é Um Shinobi! Missões Estão Fora de Alcance!");
            congelar_tela();
            i--;
            continue;
        }

        if ((VerficaRepetiçaoDeOpçao(i + 1, QuantiaAux, aux, OpçaoDeShinobi)))
        {
            limpar_tela();
            printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Esta Opçao Ja Foi Escolhida!");
            congelar_tela();
            i--;
            continue;
        }

        if (_NumeroDaMissao > 0)
        {
            int j, k, sinal;
            for (j = 0; j < _NumeroDaMissao; j++)
            {
                sinal = 0;
                for (k = 0; k < _ConjuntoDeMissoes[j].QuantidadeDeNinjas; k++)
                {
                    if ((strcmp(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Nome, _ConjuntoDeMissoes[j].NinjasDaMissao[k]->Nome) == 0))
                    {
                        if ((strcmp(_ConjuntoDeMissoes[j].Status, "Em Andamento") == 0))
                        {
                            limpar_tela();
                            printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> SHINOBI DESEJADO ]==~>\n\nErro! Este shinobi já está designado a uma missão em andamento!");
                            sinal = 1;
                            break;
                        }
                    }
                }
                if (sinal)
                {
                    break;
                }
            }
            if (sinal)
            {
                congelar_tela();
                i--;
                continue;
            }
        }

        if (OpçaoDeShinobi != 0)
        {
            CadastroDeMissao.NinjasDaMissao[i] = malloc(sizeof(tNinja));

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Nome != NULL)
            {
                CadastroDeMissao.NinjasDaMissao[i]->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Nome) + 1) * sizeof(char));
                strcpy(CadastroDeMissao.NinjasDaMissao[i]->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].Nome);
            }

            CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla = malloc(sizeof(tCla));

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->NomeDoCla != NULL)
            {
                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->NomeDoCla = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->NomeDoCla) + 1) * sizeof(char));
                strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->NomeDoCla, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->NomeDoCla);
            }

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios != NULL)
            {
                int j;
                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->QuantidadeDeJutsusHereditarios = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios;
                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios = (tJutsu **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios) + 1) * sizeof(tJutsu *));
                for (j = 0; j < (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios); j++)
                {
                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j] = malloc(sizeof(tJutsu));

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Nome != NULL)
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Nome) + 1) * sizeof(char));
                        strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Nome);
                    }

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu != NULL)
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu) + 1) * sizeof(char));
                        strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu);
                    }

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos != NULL)
                    {
                        int k;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos = (char **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos) + 1) * sizeof(char *));
                        for (k = 0; k < _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos; k++)
                        {
                            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k] != NULL)
                            {
                                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k] = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k]) + 1) * sizeof(char));
                                strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k], _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k]);
                            }
                        }
                    }
                    else
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos = 0;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->Elementos = NULL;
                    }

                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->CustodeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->CustodeChakra;

                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusHereditarios[j]->PoderdeAtaque = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusHereditarios[j]->PoderdeAtaque;
                }
            }

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais != NULL)
            {
                int j;
                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->QuantidadeDeJutsusTradicionais = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais;
                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais = (tJutsu **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais) + 1) * sizeof(tJutsu *));
                for (j = 0; j < (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais); j++)
                {
                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j] = malloc(sizeof(tJutsu));

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Nome != NULL)
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Nome) + 1) * sizeof(char));
                        strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Nome);
                    }

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu != NULL)
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu) + 1) * sizeof(char));
                        strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu);
                    }

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos != NULL)
                    {
                        int k;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos = (char **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos) + 1) * sizeof(char *));
                        for (k = 0; k < _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos; k++)
                        {
                            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k] != NULL)
                            {
                                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k] = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k]) + 1) * sizeof(char));
                                strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k], _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k]);
                            }
                        }
                    }
                    else
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos = 0;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->Elementos = NULL;
                    }

                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->CustodeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->CustodeChakra;

                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoCla->JutsusTradicionais[j]->PoderdeAtaque = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoCla->JutsusTradicionais[j]->PoderdeAtaque;
                }
            }

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente != NULL)
            {
                CadastroDeMissao.NinjasDaMissao[i]->Patente = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente) + 1) * sizeof(char));
                strcpy(CadastroDeMissao.NinjasDaMissao[i]->Patente, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].Patente);
            }

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeChakra)
            {
                CadastroDeMissao.NinjasDaMissao[i]->QuantidadeDeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeChakra;
            }

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].ElementoPrincipal != NULL)
            {
                CadastroDeMissao.NinjasDaMissao[i]->ElementoPrincipal = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].ElementoPrincipal) + 1) * sizeof(char));
                strcpy(CadastroDeMissao.NinjasDaMissao[i]->ElementoPrincipal, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].ElementoPrincipal);
            }

            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu != NULL)
            {
                int j;
                CadastroDeMissao.NinjasDaMissao[i]->QuantidadeDeJutsus = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus;
                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu = (tJutsu **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus) + 1) * sizeof(tJutsu *));
                for (j = 0; j < (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].QuantidadeDeJutsus); j++)
                {
                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j] = malloc(sizeof(tJutsu));

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Nome)
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->Nome = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Nome) + 1) * sizeof(char));
                        strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->Nome, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Nome);
                    }

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->TipoJutsu != NULL)
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->TipoJutsu = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->TipoJutsu) + 1) * sizeof(char));
                        strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->TipoJutsu, _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->TipoJutsu);
                    }

                    if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos > 0 && _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos != NULL)
                    {
                        int k;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->QuantidadeDeElementos = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->Elementos = (char **)malloc(((_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos) + 1) * sizeof(char *));
                        for (k = 0; k < _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->QuantidadeDeElementos; k++)
                        {
                            if (_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos[k])
                            {
                                CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->Elementos[k] = (char *)malloc((strlen(_ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos[k]) + 1) * sizeof(char));
                                strcpy(CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->Elementos[k], _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->Elementos[k]);
                            }
                        }
                    }
                    else
                    {
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->QuantidadeDeElementos = 0;
                        CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->Elementos = NULL;
                    }

                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->CustodeChakra = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->CustodeChakra;

                    CadastroDeMissao.NinjasDaMissao[i]->CaracteristicasDoJutsu[j]->PoderdeAtaque = _ConjuntoDeShinobis[OpçaoDeShinobi - 1].CaracteristicasDoJutsu[j]->PoderdeAtaque;
                }
            }
        }
    }

    //=======================================================================================================================================================================================//

    return CadastroDeMissao;
}

void Carregamento()
{
    int i;
    printf("Carregando");
    for (i = 0; i < 4; i++)
    {
        fflush(stdout);

#ifdef _WIN32
        Sleep(1000);
#else
        sleep(1);
#endif

        printf(".");
    }
    printf("\n");
}

void limpar_tela()
{
#ifdef _WIN32
    system("cls"); // Windows
#else
    system("clear"); // Linux / macOS
#endif
}

void congelar_tela()
{
#ifdef _WIN32
    printf("\n");
    system("pause"); // Windows
#else
    printf("\n");
    printf("Pressione Enter para continuar...");
    getchar(); // Linux / macOS
#endif
}

void Imprime(int CasoDeImpressao, int TipoDeImpressao)
{
    int i, OpçaoDeShinobi, OpçaoDeCla, OpçaoDeJutsu, OpçaoDeMissao;

    switch (TipoDeImpressao)
    {
    case 1:
    {
        if (_NumeroDoShinobi > 0)
        {
            while (1)
            {
                if (CasoDeImpressao == 1)
                {
                    limpar_tela();
                    printf("\n<~==[ EXIBIR: SHINOBI ]==~>\n");
                }

                for (i = 0; i < _NumeroDoShinobi; i++)
                {
                    printf("\n{ %d } - %s: %s %s", i + 1, _ConjuntoDeShinobis[i].Patente, _ConjuntoDeShinobis[i].Nome, _ConjuntoDeShinobis[i].CaracteristicasDoCla->NomeDoCla);
                }

                if (CasoDeImpressao == 1)
                {
                    printf("\nDeseja Ver Algum Shinobi de Maneira Detalhada?(s/n) ");
                    if (simOUnao() == 1)
                    {
                        printf("Digite o Numero do Shinobi Que Deseja: ");
                        OpçaoDeShinobi = VereficaQuantia(1, (_NumeroDoShinobi));
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: SHINOBI ]==~>\n");
                        RelatorioDaOpçao(1, (OpçaoDeShinobi), 2);
                    }
                    else
                    {
                        printf("Deseja Voltar Para o Menu Exibir?(s/n) ");
                        if (simOUnao() == 1)
                        {
                            Carregamento();
                            break;
                        }
                        else
                        {
                            continue;
                        }
                    }
                }
                else
                {
                    break;
                }
            }
        }
        else
        {
            NovoCadastro(1, 1);
        }
        break;
    }

    case 2:
    {
        if (_NumeroDoCla > 0)
        {
            while (1)
            {
                if (CasoDeImpressao == 1)
                {
                    limpar_tela();
                    printf("\n<~==[ EXIBIR: CLA ]==~>\n");
                }

                for (i = 0; i < _NumeroDoCla; i++)
                {
                    printf("\n{ %d } - %s", i + 1, _ConjuntoDeClas[i].NomeDoCla);
                }

                if (CasoDeImpressao == 1)
                {
                    printf("\nDeseja Ver Algum Cla de Maneira Mais Detalhada?(s/n) ");
                    if (simOUnao() == 1)
                    {
                        printf("Digite o Numero do Cla Que Deseja: ");
                        OpçaoDeCla = VereficaQuantia(1, (_NumeroDoCla));
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: CLA ]==~>\n");
                        RelatorioDaOpçao(2, (OpçaoDeCla), 2);
                    }
                    else
                    {
                        printf("Deseja Voltar Para o Menu Exibir?(s/n) ");
                        if (simOUnao() == 1)
                        {
                            Carregamento();
                            break;
                        }
                        else
                        {
                            continue;
                        }
                    }
                }
                else
                {
                    break;
                }
            }
        }
        else
        {
            NovoCadastro(2, 1);
        }
        break;
    }

    case 3:
    {
        if (_NumeroDoJutsu > 0)
        {
            while (1)
            {
                if (CasoDeImpressao == 1)
                {
                    limpar_tela();
                    printf("\n<~==[ EXIBIR: JUTSU ]==~>\n");
                }

                for (i = 0; i < _NumeroDoJutsu; i++)
                {
                    printf("\n{ %d } - %s: %s", i + 1, _ConjuntoDeJutsus[i].TipoJutsu, _ConjuntoDeJutsus[i].Nome);
                }

                if (CasoDeImpressao == 1)
                {
                    printf("\nDeseja Ver Algum de Maneira Mais Detalhada?(s/n) ");
                    if (simOUnao() == 1)
                    {
                        printf("Digite o Numero do Jutsu Que Deseja: ");
                        OpçaoDeJutsu = VereficaQuantia(1, (_NumeroDoJutsu));
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: JUTSU ]==~>\n");
                        RelatorioDaOpçao(3, (OpçaoDeJutsu), 2);
                        congelar_tela();
                    }
                    else
                    {
                        printf("Deseja Voltar Para o Menu Exibir?(s/n) ");
                        if (simOUnao() == 1)
                        {
                            Carregamento();
                            break;
                        }
                        else
                        {
                            continue;
                        }
                    }
                }
                else
                {
                    break;
                }
            }
        }
        else
        {
            NovoCadastro(3, 1);
        }
        break;
    }

    case 4:
    {
        if (_NumeroDaMissao > 0)
        {
            while (1)
            {
                if (CasoDeImpressao == 1)
                {
                    limpar_tela();
                    printf("\n<~==[ EXIBIR: MISSAO ]==~>\n");
                }

                for (i = 0; i < _NumeroDaMissao; i++)
                {
                    printf("\n{ %d } - Nivel %s: %s", i + 1, _ConjuntoDeMissoes[i].NivelDificuldade, _ConjuntoDeMissoes[i].Titulo);
                }

                if (CasoDeImpressao == 1)
                {
                    printf("\nDeseja Ver Alguma de Maneira Mais Detalhada?(s/n) ");
                    if (simOUnao() == 1)
                    {
                        printf("Digite o Numero da Missao Que Deseja: ");
                        OpçaoDeMissao = VereficaQuantia(1, (_NumeroDaMissao));
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: MISSAO ]==~>\n");
                        RelatorioDaOpçao(4, (OpçaoDeMissao), 2);
                        congelar_tela();
                    }
                    else
                    {
                        printf("Deseja Voltar Para o Menu Exibir?(s/n) ");
                        if (simOUnao() == 1)
                        {
                            Carregamento();
                            break;
                        }
                        else
                        {
                            continue;
                        }
                    }
                }
                else
                {
                    break;
                }
            }
        }
        else
        {
            NovoCadastro(4, 1);
        }
        break;
    }
    }
}

void RelatorioDaOpçao(int Contexto, int OpçaoDeContexto, int Necessidade)
{
    int j;

    switch (Contexto)
    {
    case 1:
        printf("\n=================================\n(RELATORIO DO SHINOBI SELECIONADO)\n=================================");

        // Verificação extra de segurança
        if (OpçaoDeContexto - 1 < 0 || OpçaoDeContexto - 1 >= _NumeroDoShinobi)
        {
            printf("\nErro: Shinobi invalido ou nao encontrado!");
            printf("\n=================================\n");
            break;
        }

        printf("\nNome do Shinobi: (%s)", _ConjuntoDeShinobis[OpçaoDeContexto - 1].Nome);

        // Verificação para CaracteristicasDoCla
        if (_ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoCla != NULL &&
            _ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoCla->NomeDoCla != NULL)
        {
            printf("\nCla do Shinobi: (%s)", _ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoCla->NomeDoCla);
        }
        else
        {
            printf("\nCla do Shinobi: (Nenhum)");
        }

        printf("\nPatente do Shinobi: (%s)", _ConjuntoDeShinobis[OpçaoDeContexto - 1].Patente);
        printf("\nReserva de Chakra: (%d)", _ConjuntoDeShinobis[OpçaoDeContexto - 1].QuantidadeDeChakra);
        printf("\nElemento Principal: (%s)", _ConjuntoDeShinobis[OpçaoDeContexto - 1].ElementoPrincipal);

        // JUTSUS COM VERIFICAÇÕES
        if (_ConjuntoDeShinobis[OpçaoDeContexto - 1].QuantidadeDeJutsus > 0 &&
            _ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoJutsu != NULL)
        {
            printf("\nJutsus Dominados: (");
            int primeiro = 1;
            for (j = 0; j < (_ConjuntoDeShinobis[OpçaoDeContexto - 1].QuantidadeDeJutsus); j++)
            {
                if (_ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoJutsu[j] != NULL &&
                    _ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoJutsu[j]->Nome != NULL)
                {
                    if (!primeiro)
                    {
                        printf(", ");
                    }
                    printf("%s", _ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoJutsu[j]->Nome);
                    primeiro = 0;
                }
            }
            printf(")");
        }
        else
        {
            printf("\nJutsus Dominados: (Nenhum)");
        }
        printf("\n=================================\n");

        if (Necessidade == 2)
        {
            printf("\nDeseja Ver O Cla Ou Algum Jutsu de Maneira Mais Detalhada?(s/n) ");
            if (simOUnao() == 1)
            {
                int i;
                int OpçaoDeJutsu;
                int OpçaoEscolhida;

                while (1)
                {
                    limpar_tela();
                    printf("\n<~==[ EXIBIR: INFORMAÇOES DO SHINOBI ]==~>\n");
                    printf("\nEscolha Uma Opcao:\n\n{ 1 } - Cla\n{ 2 } - Jutsu\n{ 3 } - Voltar\nOpcao: ");
                    OpçaoEscolhida = VereficaQuantia(1, 3);
                    if (OpçaoEscolhida == 1)
                    {
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: CLA DO SHINOBI ]==~>\n");
                        RelatorioDaOpçao(2, OpçaoDeContexto, 2);
                        congelar_tela();
                        continue;
                    }
                    else if (OpçaoEscolhida == 2)
                    {
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: JUTSUS SHINOBI ]==~>\n");
                        if (_ConjuntoDeShinobis[OpçaoDeContexto - 1].QuantidadeDeJutsus > 0)
                        {
                            for (i = 0; i < _ConjuntoDeShinobis[OpçaoDeContexto - 1].QuantidadeDeJutsus; i++)
                            {
                                printf("\n{ %d } - %s", i + 1, _ConjuntoDeShinobis[OpçaoDeContexto - 1].CaracteristicasDoJutsu[i]->Nome);
                            }
                            printf("\nDigite o Numero do Jutsu Que Deseja: ");
                            OpçaoDeJutsu = VereficaQuantia(1, (_ConjuntoDeShinobis[OpçaoDeContexto - 1].QuantidadeDeJutsus));
                            limpar_tela();
                            printf("\n<~==[ EXIBIR: JUTSU ESPECIFICO DO SHINOBI ]==~>\n");
                            RelatorioDaOpçao(3, (OpçaoDeJutsu), 1);
                            congelar_tela();
                            continue;
                        }
                        else
                        {
                            printf("\nEste Shinobi Nao Domina Nenhum Jutsu Especifico!");
                            congelar_tela();
                            continue;
                        }
                    }
                    else if (OpçaoEscolhida == 3)
                    {
                        break;
                    }
                }
            }
        }
        break;

    case 2:
        printf("\n=================================\n(RELATORIO DO CLA SELECIONADO)\n=================================");

        // Verificação extra de segurança
        if (OpçaoDeContexto - 1 < 0 || OpçaoDeContexto - 1 >= _NumeroDoCla)
        {
            printf("\nErro: Cla invalido ou nao encontrado!");
            printf("\n=================================\n");
            break;
        }

        printf("\nNome do Cla: (%s)", _ConjuntoDeClas[OpçaoDeContexto - 1].NomeDoCla);

        // JUTSUS HEREDITARIOS COM VERIFICAÇÕES
        if (_ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusHereditarios > 0 &&
            _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusHereditarios != NULL)
        {
            printf("\nJutsus Hereditarios Do Cla: (");
            int primeiro = 1;
            for (j = 0; j < _ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusHereditarios; j++)
            {
                if (_ConjuntoDeClas[OpçaoDeContexto - 1].JutsusHereditarios[j] != NULL &&
                    _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusHereditarios[j]->Nome != NULL)
                {
                    if (!primeiro)
                    {
                        printf(", ");
                    }
                    printf("%s", _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusHereditarios[j]->Nome);
                    primeiro = 0;
                }
            }
            printf(")");
        }
        else
        {
            printf("\nJutsus Hereditarios Do Cla: (Nenhum Jutsu)");
        }

        // JUTSUS TRADICIONAIS COM VERIFICAÇÕES
        if (_ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusTradicionais > 0 &&
            _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusTradicionais != NULL)
        {
            printf("\nJutsus Tradicionais Do Cla: (");
            int primeiro = 1;
            for (j = 0; j < _ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusTradicionais; j++)
            {
                if (_ConjuntoDeClas[OpçaoDeContexto - 1].JutsusTradicionais[j] != NULL &&
                    _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusTradicionais[j]->Nome != NULL)
                {
                    if (!primeiro)
                    {
                        printf(", ");
                    }
                    printf("%s", _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusTradicionais[j]->Nome);
                    primeiro = 0;
                }
            }
            printf(")");
        }
        else
        {
            printf("\nJutsus Tradicionais Do Cla: (Nenhum Jutsu)");
        }
        printf("\n=================================\n");

        if (Necessidade == 2)
        {
            printf("\nDeseja Ver os Membros ou os Jutsus De Maneira Mais Detalhada?(s/n) ");
            if (simOUnao() == 1)
            {
                int OpçaoEscolhida;
                int OpçaoDeJutsu;
                int i;

                while (1)
                {
                    limpar_tela();
                    printf("\n<~==[ EXIBIR: INFORMAÇOES DO CLA ]==~>\n");
                    printf("\nEscolha Uma Opcao:\n\n{ 1 } - Jutsus Hereditarios\n{ 2 } - Jutsus Tradicionais\n{ 3 } - Membros\n{ 4 } - Voltar\nOpcao: ");
                    OpçaoEscolhida = VereficaQuantia(1, 4);
                    if (OpçaoEscolhida == 1)
                    {
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: JUTSUS HEREDITARIOS DO CLA ]==~>\n");
                        if (_ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusHereditarios > 0)
                        {
                            for (i = 0; i < _ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusHereditarios; i++)
                            {
                                printf("\n{ %d } - %s", i + 1, _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusHereditarios[i]->Nome);
                            }
                            printf("\nDigite o Numero do Jutsu Que Deseja: ");
                            OpçaoDeJutsu = VereficaQuantia(1, (_ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusHereditarios));
                            limpar_tela();
                            printf("\n<~==[ EXIBIR: JUTSU HEREDITARIO ESPECIFICO DO CLA ]==~>\n");
                            RelatorioDaOpçao(3, (OpçaoDeJutsu), 1);
                            congelar_tela();
                            continue;
                        }
                        else
                        {
                            printf("\nEste Cla Nao Possui Nenhum Jutsu Hereditario");
                            congelar_tela();
                            continue;
                        }
                    }
                    else if (OpçaoEscolhida == 2)
                    {
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: JUTSUS TRADICIONAIS DO CLA ]==~>\n");
                        if (_ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusTradicionais > 0)
                        {
                            for (i = 0; i < _ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusTradicionais; i++)
                            {
                                printf("\n{ %d } - %s", i + 1, _ConjuntoDeClas[OpçaoDeContexto - 1].JutsusTradicionais[i]->Nome);
                            }
                            printf("\nDigite o Numero do Jutsu Que Deseja: ");
                            OpçaoDeJutsu = VereficaQuantia(1, (_ConjuntoDeClas[OpçaoDeContexto - 1].QuantidadeDeJutsusTradicionais));
                            limpar_tela();
                            printf("\n<~==[ EXIBIR: JUTSU TRADICIONAL ESPECIFICO DO CLA ]==~>\n");
                            RelatorioDaOpçao(3, (OpçaoDeJutsu), 1);
                            congelar_tela();
                            continue;
                        }
                        else
                        {
                            printf("\nEste Cla Nao Possui Nenhum Jutsu Tradicional");
                            congelar_tela();
                            continue;
                        }
                    }
                    else if (OpçaoEscolhida == 3)
                    {
                        int *aux = calloc(101, sizeof(int));
                        int ctt;
                        int OpçaoDeMembro;
                        for (i = 0; 1; i++)
                        {
                            if (_NumeroDoShinobi > 0)
                            {
                                i = 0;
                                ctt = 0;
                                limpar_tela();
                                printf("\n<~==[ EXIBIR: MEMBROS DO CLA ]==~>\n");
                                for (j = 0; j < _NumeroDoShinobi; j++)
                                {
                                    if (strcmp(_ConjuntoDeClas[OpçaoDeContexto - 1].NomeDoCla, _ConjuntoDeShinobis[j].CaracteristicasDoCla->NomeDoCla) == 0)
                                    {
                                        printf("\n{ %d } - %s", i + 1, _ConjuntoDeShinobis[j].Nome);
                                        aux[i] = j;
                                        ctt++;
                                    }
                                }
                                printf("\nDeseja Ver Algum Membro De Maneira Mais Detalhada?(s/n) ");
                                if (simOUnao() == 1)
                                {
                                    printf("\nDigite o Numero do Membro Que Deseja: ");
                                    OpçaoDeMembro = VereficaQuantia(1, ctt);
                                    printf("\n<~==[ EXIBIR: MEMBRO ESPECIFICO DO CLA ]==~>\n");
                                    RelatorioDaOpçao(1, (aux[OpçaoDeMembro - 1] + 1), 2);
                                    congelar_tela();
                                    continue;
                                }
                                else
                                {
                                    break;
                                }
                            }
                            else
                            {
                                limpar_tela();
                                printf("\n<~==[ EXIBIR: MEMBROS DO CLA ]==~>\n");
                                printf("\nNao ha Shinobis Que Tenham Parentesco Com Este Cla Cadastrados!");
                                congelar_tela();
                                break;
                            }
                        }
                    }
                    else
                    {
                        break;
                    }
                }
            }
        }
        break;

    case 3:
        printf("\n=================================\n(RELATORIO DO JUTSU SELECIONADO)\n=================================");

        // Verificação extra de segurança
        if (OpçaoDeContexto - 1 < 0 || OpçaoDeContexto - 1 >= _NumeroDoJutsu)
        {
            printf("\nErro: Jutsu invalido ou nao encontrado!");
            printf("\n=================================\n");
            break;
        }

        printf("\nNome do Jutsu: (%s)", _ConjuntoDeJutsus[OpçaoDeContexto - 1].Nome);
        printf("\nTipo do Jutsu: (%s)", _ConjuntoDeJutsus[OpçaoDeContexto - 1].TipoJutsu);

        // ELEMENTOS COM VERIFICAÇÕES
        if (_ConjuntoDeJutsus[OpçaoDeContexto - 1].QuantidadeDeElementos > 0 &&
            _ConjuntoDeJutsus[OpçaoDeContexto - 1].Elementos != NULL)
        {
            printf("\nElementos Presentes no Jutsu: (");
            int primeiro = 1;
            for (j = 0; j < _ConjuntoDeJutsus[OpçaoDeContexto - 1].QuantidadeDeElementos; j++)
            {
                if (_ConjuntoDeJutsus[OpçaoDeContexto - 1].Elementos[j] != NULL)
                {
                    if (!primeiro)
                    {
                        printf(", ");
                    }
                    printf("%s", _ConjuntoDeJutsus[OpçaoDeContexto - 1].Elementos[j]);
                    primeiro = 0;
                }
            }
            printf(")");
        }
        else
        {
            printf("\nElementos Presentes: (Nenhum Elemento)");
        }
        printf("\nCusto de Chakra do Jutsu: (%d)", _ConjuntoDeJutsus[OpçaoDeContexto - 1].CustodeChakra);
        printf("\nPoder de Ataque do Jutsu: (%d)", _ConjuntoDeJutsus[OpçaoDeContexto - 1].PoderdeAtaque);
        printf("\n=================================\n");
        break;

    case 4:
        printf("\n=================================\n(RELATORIO DA MISSAO SELECIONADA)\n=================================");

        // Verificação extra de segurança
        if (OpçaoDeContexto - 1 < 0 || OpçaoDeContexto - 1 >= _NumeroDaMissao)
        {
            printf("\nErro: Missao invalida ou nao encontrada!");
            printf("\n=================================\n");
            break;
        }

        printf("\nTitulo da Missao: (%s)", _ConjuntoDeMissoes[OpçaoDeContexto - 1].Titulo);
        printf("\nLider da Missao: (%s)", _ConjuntoDeMissoes[OpçaoDeContexto - 1].LiderResponsavel->Nome);
        printf("\nNivel da Missao: (%s)", _ConjuntoDeMissoes[OpçaoDeContexto - 1].NivelDificuldade);
        printf("\nStatus da Missao: (%s)", _ConjuntoDeMissoes[OpçaoDeContexto - 1].Status);
        if (_ConjuntoDeMissoes[OpçaoDeContexto - 1].QuantidadeDeNinjas > 0 &&
            _ConjuntoDeMissoes[OpçaoDeContexto - 1].NinjasDaMissao != NULL)
        {
            printf("\nShinobis Presentes na Missao: (");
            int primeiro = 1;
            for (j = 0; j < _ConjuntoDeMissoes[OpçaoDeContexto - 1].QuantidadeDeNinjas; j++)
            {
                if (_ConjuntoDeMissoes[OpçaoDeContexto - 1].NinjasDaMissao[j]->Nome != NULL)
                {
                    if (!primeiro)
                    {
                        printf(", ");
                    }
                    printf("%s", _ConjuntoDeMissoes[OpçaoDeContexto - 1].NinjasDaMissao[j]->Nome);
                    primeiro = 0;
                }
            }
            printf(")");
        }
        else
        {
            printf("\nShinobi Presentes: (Nenhum Shinobi)");
        }
        printf("\nQuantidade de Ninjas da Missao: (%d)", _ConjuntoDeMissoes[OpçaoDeContexto - 1].QuantidadeDeNinjas);
        printf("\n=================================\n");

        if (Necessidade == 2)
        {
            printf("\nDeseja Ver os Shinobis da Missao ou o Lider De Maneira Mais Detalhada?(s/n) ");
            if (simOUnao() == 1)
            {
                while (1)
                {
                    int OpçaoEscolhida;
                    int OpçaoDeShinobi;
                    int i;

                    limpar_tela();
                    printf("\n<~==[ EXIBIR: INFORMAÇOES DA MISSAO ]==~>\n");
                    printf("\nEscolha Uma Opcao:\n\n{ 1 } - Lider Da Missao\n{ 2 } - Shinobis Da Missao\n{ 3 } - Voltar\nOpcao: ");
                    OpçaoEscolhida = VereficaQuantia(1, 3);

                    if (OpçaoEscolhida == 1)
                    {
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: LIDER DA MISSAO ]==~>\n");
                        RelatorioDaOpçao(1, OpçaoDeContexto, 2);
                        congelar_tela();
                    }
                    else if (OpçaoEscolhida == 2)
                    {
                        limpar_tela();
                        printf("\n<~==[ EXIBIR: SHINOBIS DA MISSAO ]==~>\n");
                        if (_ConjuntoDeMissoes[OpçaoDeContexto - 1].QuantidadeDeNinjas > 0)
                        {
                            for (i = 0; i < _ConjuntoDeMissoes[OpçaoDeContexto - 1].QuantidadeDeNinjas; i++)
                            {
                                printf("\n{ %d } - %s", i + 1, _ConjuntoDeMissoes[OpçaoDeContexto - 1].NinjasDaMissao[i]->Nome);
                            }
                            printf("\nDigite o Numero do Shinobi Que Deseja: ");
                            OpçaoDeShinobi = VereficaQuantia(1, (_ConjuntoDeMissoes[OpçaoDeContexto - 1].QuantidadeDeNinjas));
                            limpar_tela();
                            printf("\n<~==[ EXIBIR: SHINOBI ESPECIFICO DA MISSAO ]==~>\n");
                            RelatorioDaOpçao(1, (OpçaoDeShinobi), 2);
                            congelar_tela();
                        }
                    }
                    else
                    {
                        break;
                    }
                }
            }
        }
        break;
    }
}

void AlterarRegistro(int TipoDeContexto, int indice)
{
    int opcao;
    char StrAux[100];

    do
    {
        limpar_tela();
        printf("\n<~==[ ALTERAR REGISTRO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");

        // Exibir dados atuais
        ExibirDadosAtuais(TipoDeContexto, indice);

        printf("\nO que deseja alterar?\n");

        switch (TipoDeContexto)
        {
        case 1: // Shinobi
            printf("{ 1 } - Nome\n{ 2 } - Patente\n{ 3 } - Reserva de Chakra\n{ 4 } - Elemento Principal\n{ 5 } - Cla\n{ 6 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opcao: ");
            opcao = VereficaQuantia(1, 6);

            switch (opcao)
            {
            case 1:
                limpar_tela();
                printf("\n<~==[ ALTERAR: NOME DO SHINOBI ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNome atual: %s\nNovo nome: ", _ConjuntoDeShinobis[indice].Nome);
                VerificadorDeTexto(StrAux);
                free(_ConjuntoDeShinobis[indice].Nome);
                _ConjuntoDeShinobis[indice].Nome = malloc(strlen(StrAux) + 1);
                strcpy(_ConjuntoDeShinobis[indice].Nome, StrAux);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNome alterado com sucesso!\n");
                break;

            case 2:
                limpar_tela();
                printf("\n<~==[ ALTERAR: PATENTE DO SHINOBI ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nPatente atual: %s\nNova patente:\n{ 1 } Estudante da Academia\n{ 2 } Genin\n{ 3 } Chunin\n{ 4 } Tokubetsu Jounin\n{ 5 } Jounin\n{ 6 } ANBU\n{ 7 } Kage\nEscolha uma opcao: ", _ConjuntoDeShinobis[indice].Patente);
                SelecionaPatente(VereficaQuantia(1, 7), StrAux);
                free(_ConjuntoDeShinobis[indice].Patente);
                _ConjuntoDeShinobis[indice].Patente = malloc(strlen(StrAux) + 1);
                strcpy(_ConjuntoDeShinobis[indice].Patente, StrAux);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nPatente alterada com sucesso!\n");
                break;

            case 3:
                limpar_tela();
                printf("\n<~==[ ALTERAR: RESERVA DE CHAKRA ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nReserva atual: %d\nNova reserva de chakra (1-100): ", _ConjuntoDeShinobis[indice].QuantidadeDeChakra);
                _ConjuntoDeShinobis[indice].QuantidadeDeChakra = VereficaQuantia(1, 100);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nChakra alterado com sucesso!\n");
                break;

            case 4:
                limpar_tela();
                printf("\n<~==[ ALTERAR: ELEMENTO PRINCIPAL ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nElemento atual: %s\nNovo elemento principal: ", _ConjuntoDeShinobis[indice].ElementoPrincipal);
                VerificadorDeTexto(StrAux);
                free(_ConjuntoDeShinobis[indice].ElementoPrincipal);
                _ConjuntoDeShinobis[indice].ElementoPrincipal = malloc(strlen(StrAux) + 1);
                strcpy(_ConjuntoDeShinobis[indice].ElementoPrincipal, StrAux);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nElemento alterado com sucesso!\n");
                break;

            case 5:
                limpar_tela();
                printf("\n<~==[ ALTERAR: CLA DO SHINOBI ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");

                if (_NumeroDoCla == 0)
                {
                    printf("Nenhum cla cadastrado no momento!\nE necessario cadastrar um cla primeiro.\n");
                }
                else
                {
                    printf("Cla atual: ");
                    if (_ConjuntoDeShinobis[indice].CaracteristicasDoCla != NULL && _ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla != NULL)
                    {
                        printf("%s\n", _ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla);
                    }
                    else
                    {
                        printf("Nenhum\n");
                    }

                    printf("\nClas disponiveis:\n");
                    for (int i = 0; i < _NumeroDoCla; i++)
                    {
                        printf("{ %d } - %s\n", i + 1, _ConjuntoDeClas[i].NomeDoCla);
                    }

                    printf("\nSelecione o novo cla (0 para remover o cla atual): ");
                    int opcaoCla = VereficaQuantia(0, _NumeroDoCla);

                    if (opcaoCla == 0)
                    {
                        if (_ConjuntoDeShinobis[indice].CaracteristicasDoCla != NULL)
                        {
                            if (_ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla != NULL)
                            {
                                free(_ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla);
                            }
                            free(_ConjuntoDeShinobis[indice].CaracteristicasDoCla);
                            _ConjuntoDeShinobis[indice].CaracteristicasDoCla = NULL;
                        }
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCla removido com sucesso!\n");
                    }
                    else
                    {
                        if (_ConjuntoDeShinobis[indice].CaracteristicasDoCla == NULL)
                        {
                            _ConjuntoDeShinobis[indice].CaracteristicasDoCla = malloc(sizeof(tCla));
                        }
                        else
                        {
                            if (_ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla != NULL)
                            {
                                free(_ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla);
                            }
                        }

                        _ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla = malloc(strlen(_ConjuntoDeClas[opcaoCla - 1].NomeDoCla) + 1);
                        strcpy(_ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla, _ConjuntoDeClas[opcaoCla - 1].NomeDoCla);

                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCla alterado para: %s\n", _ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla);
                    }
                }
                break;
            }
            break;

        case 2: // Clã
            printf("{ 1 } - Nome do Cla\n{ 2 } - Jutsus Hereditarios\n{ 3 } - Jutsus Tradicionais\n{ 4 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opcao: ");
            opcao = VereficaQuantia(1, 4);

            switch (opcao)
            {
            case 1:
                limpar_tela();
                printf("\n<~==[ ALTERAR: NOME DO CLA ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNome atual: %s\nNovo nome do cla: ", _ConjuntoDeClas[indice].NomeDoCla);
                VerificadorDeTexto(StrAux);
                free(_ConjuntoDeClas[indice].NomeDoCla);
                _ConjuntoDeClas[indice].NomeDoCla = malloc(strlen(StrAux) + 1);
                strcpy(_ConjuntoDeClas[indice].NomeDoCla, StrAux);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNome do cla alterado com sucesso!\n");
                break;

            case 2:
                if (_ConjuntoDeClas[indice].JutsusHereditarios == NULL)
                {
                    printf("\n<~==[ ALTERAR: JUTSUS HEREDITARIOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nErro: Array de jutsus nao inicializado!\n");
                }
                else if (_ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios == 0)
                {
                    printf("\n<~==[ ALTERAR: JUTSUS HEREDITARIOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNenhum jutsu hereditario cadastrado.\n");
                }
                else
                {
                    limpar_tela();
                    printf("\n<~==[ ALTERAR: JUTSUS HEREDITARIOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsus Hereditarios:\n");
                    for (int i = 0; i < _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios; i++)
                    {
                        if (_ConjuntoDeClas[indice].JutsusHereditarios[i] != NULL)
                        {
                            printf("{ %d } - %s\n", i + 1, _ConjuntoDeClas[indice].JutsusHereditarios[i]->Nome);
                        }
                    }
                    printf("Digite o numero do jutsu: ");
                    int jutsu_idx = VereficaQuantia(1, _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios) - 1;
                    if (_ConjuntoDeClas[indice].JutsusHereditarios[jutsu_idx] != NULL)
                    {
                        AlterarJutsuEspecifico(_ConjuntoDeClas[indice].JutsusHereditarios[jutsu_idx]);
                    }
                    else
                    {
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nErro: Jutsu invalido!\n");
                    }
                }
                break;

            case 3:
                if (_ConjuntoDeClas[indice].JutsusTradicionais == NULL)
                {
                    printf("\n<~==[ ALTERAR: JUTSUS TRADICIONAIS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nErro: Array de jutsus nao inicializado!\n");
                }
                else if (_ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais == 0)
                {
                    printf("\n<~==[ ALTERAR: JUTSUS TRADICIONAIS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNenhum jutsu tradicional cadastrado.\n");
                }
                else
                {
                    limpar_tela();
                    printf("\n<~==[ ALTERAR: JUTSUS TRADICIONAIS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsus Tradicionais:\n");
                    for (int i = 0; i < _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais; i++)
                    {
                        if (_ConjuntoDeClas[indice].JutsusTradicionais[i] != NULL)
                        {
                            printf("{ %d } - %s\n", i + 1, _ConjuntoDeClas[indice].JutsusTradicionais[i]->Nome);
                        }
                    }
                    printf("Digite o numero do jutsu: ");
                    int jutsu_idx = VereficaQuantia(1, _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais) - 1;
                    if (_ConjuntoDeClas[indice].JutsusTradicionais[jutsu_idx] != NULL)
                    {
                        AlterarJutsuEspecifico(_ConjuntoDeClas[indice].JutsusTradicionais[jutsu_idx]);
                    }
                    else
                    {
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nErro: Jutsu invalido!\n");
                    }
                }
                break;
            }
            break;

        case 3: // Jutsu
            printf("{ 1 } - Nome\n{ 2 } - Tipo\n{ 3 } - Custo de Chakra\n{ 4 } - Poder de Ataque\n{ 5 } - Elementos\n{ 6 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opcao: ");
            opcao = VereficaQuantia(1, 6);

            switch (opcao)
            {
            case 1:
                limpar_tela();
                printf("\n<~==[ ALTERAR: NOME DO JUTSU ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNome atual: %s\nNovo nome: ", _ConjuntoDeJutsus[indice].Nome);
                VerificadorDeTexto(StrAux);
                free(_ConjuntoDeJutsus[indice].Nome);
                _ConjuntoDeJutsus[indice].Nome = malloc(strlen(StrAux) + 1);
                strcpy(_ConjuntoDeJutsus[indice].Nome, StrAux);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNome alterado com sucesso!\n");
                break;

            case 2:
                limpar_tela();
                printf("\n<~==[ ALTERAR: TIPO DO JUTSU ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nTipo atual: %s\nNovo tipo: ", _ConjuntoDeJutsus[indice].TipoJutsu);
                VerificadorDeTexto(StrAux);
                free(_ConjuntoDeJutsus[indice].TipoJutsu);
                _ConjuntoDeJutsus[indice].TipoJutsu = malloc(strlen(StrAux) + 1);
                strcpy(_ConjuntoDeJutsus[indice].TipoJutsu, StrAux);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nTipo alterado com sucesso!\n");
                break;

            case 3:
                limpar_tela();
                printf("\n<~==[ ALTERAR: CUSTO DE CHAKRA ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCusto atual: %d\nNovo custo de chakra (1-100): ", _ConjuntoDeJutsus[indice].CustodeChakra);
                _ConjuntoDeJutsus[indice].CustodeChakra = VereficaQuantia(1, 100);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCusto de chakra alterado com sucesso!\n");
                break;

            case 4:
                limpar_tela();
                printf("\n<~==[ ALTERAR: PODER DE ATAQUE ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nPoder atual: %d\nNovo poder de ataque (1-100): ", _ConjuntoDeJutsus[indice].PoderdeAtaque);
                _ConjuntoDeJutsus[indice].PoderdeAtaque = VereficaQuantia(1, 100);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nPoder de ataque alterado com sucesso!\n");
                break;

            case 5:
                if (_ConjuntoDeJutsus[indice].QuantidadeDeElementos > 0 && _ConjuntoDeJutsus[indice].Elementos != NULL)
                {
                    limpar_tela();
                    printf("\n<~==[ ALTERAR: ELEMENTOS DO JUTSU ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nElementos atuais:\n");
                    for (int i = 0; i < _ConjuntoDeJutsus[indice].QuantidadeDeElementos; i++)
                    {
                        if (_ConjuntoDeJutsus[indice].Elementos[i] != NULL)
                        {
                            printf("{ %d } - %s\n", i + 1, _ConjuntoDeJutsus[indice].Elementos[i]);
                        }
                    }
                    printf("Digite o numero do elemento: ");
                    int elem_idx = VereficaQuantia(1, _ConjuntoDeJutsus[indice].QuantidadeDeElementos) - 1;
                    printf("Novo nome: ");
                    VerificadorDeTexto(StrAux);
                    free(_ConjuntoDeJutsus[indice].Elementos[elem_idx]);
                    _ConjuntoDeJutsus[indice].Elementos[elem_idx] = malloc(strlen(StrAux) + 1);
                    strcpy(_ConjuntoDeJutsus[indice].Elementos[elem_idx], StrAux);
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nElemento alterado com sucesso!\n");
                }
                else
                {
                    printf("\n<~==[ ALTERAR: ELEMENTOS DO JUTSU ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNenhum elemento para alterar.\n");
                }
                break;
            }
            break;

        case 4: // Missão
            printf("{ 1 } - Alterar Dados da Missao\n{ 2 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opcao: ");
            opcao = VereficaQuantia(1, 2);

            switch (opcao)
            {
            case 1:
                AlterarMissaoEspecifica(&_ConjuntoDeMissoes[indice]);
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nAlteracoes na missao concluidas!\n");
                break;
            }
            break;
        }

        // Verificar se deve sair
        if ((TipoDeContexto == 1 && opcao == 6) || (TipoDeContexto == 2 && opcao == 4) || (TipoDeContexto == 3 && opcao == 6) || (TipoDeContexto == 4 && opcao == 2))
        {
            break;
        }

        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nDeseja alterar mais algo neste registro? (s/n): ");
        if (simOUnao() == 0)
            break;

    } while (1);

    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nAlteracoes concluidas!\n");
    congelar_tela();
}

void Alterar(int TipoDeContexto)
{
    int indice;
    int limite;

    while (1)
    {
        limpar_tela();
        printf("\n<~==[ ALTERAR REGISTRO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");

        // Determinar o limite baseado no tipo de contexto
        switch (TipoDeContexto)
        {
        case 1: // Shinobi
            if (_NumeroDoShinobi == 0)
            {
                printf("\nNenhum shinobi cadastrado!\n");
                congelar_tela();
                return;
            }
            printf("Shinobis cadastrados:\n");
            for (int i = 0; i < _NumeroDoShinobi; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeShinobis[i].Nome);
            }
            limite = _NumeroDoShinobi;
            break;

        case 2: // Clã
            if (_NumeroDoCla == 0)
            {
                printf("\nNenhum clã cadastrado!\n");
                congelar_tela();
                return;
            }
            printf("Clãs cadastrados:\n");
            for (int i = 0; i < _NumeroDoCla; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeClas[i].NomeDoCla);
            }
            limite = _NumeroDoCla;
            break;

        case 3: // Jutsu
            if (_NumeroDoJutsu == 0)
            {
                printf("\nNenhum jutsu cadastrado!\n");
                congelar_tela();
                return;
            }
            printf("Jutsus cadastrados:\n");
            for (int i = 0; i < _NumeroDoJutsu; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeJutsus[i].Nome);
            }
            limite = _NumeroDoJutsu;
            break;

        case 4: // Missão
            if (_NumeroDaMissao == 0)
            {
                printf("\nNenhuma missão cadastrada!\n");
                congelar_tela();
                return;
            }
            printf("Missões cadastradas:\n");
            for (int i = 0; i < _NumeroDaMissao; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeMissoes[i].Titulo);
            }
            limite = _NumeroDaMissao;
            break;

        default:
            printf("\nTipo de contexto inválido!\n");
            congelar_tela();
            return;
        }

        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nSelecione o registro para alterar:\n{ 0 } - Voltar ao Menu Anterior\nOpção: ");
        indice = VereficaQuantia(0, limite);

        if (indice == 0)
        {
            limpar_tela();
            return; // Usuário escolheu voltar
        }

        AlterarRegistro(TipoDeContexto, indice - 1);

        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nDeseja alterar outro registro? (s/n): ");
        if (simOUnao() == 0)
        {
            limpar_tela();
            break;
        }
    }
}

void AlterarJutsuEspecifico(tJutsu *jutsu)
{
    int opcao;
    char StrAux[100];

    do
    {
        limpar_tela();
        printf("\n<~==[ ALTERAR JUTSU: %s ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n", jutsu->Nome);

        printf("Dados atuais:\n- Nome: %s\n- Tipo: %s\n", jutsu->Nome, jutsu->TipoJutsu);

        // Mostrar elementos atuais
        if (jutsu->QuantidadeDeElementos > 0 && jutsu->Elementos != NULL)
        {
            printf("- Elementos: ");
            for (int i = 0; i < jutsu->QuantidadeDeElementos; i++)
            {
                if (jutsu->Elementos[i] != NULL)
                {
                    printf("%s", jutsu->Elementos[i]);
                    if (i < jutsu->QuantidadeDeElementos - 1)
                        printf(", ");
                }
            }
            printf("\n");
        }
        else
        {
            printf("- Elementos: Nenhum\n");
        }

        printf("- Custo de Chakra: %d\n- Poder de Ataque: %d\n", jutsu->CustodeChakra, jutsu->PoderdeAtaque);

        printf("\nO que deseja alterar?\n{ 1 } Nome\n{ 2 } Tipo\n{ 3 } Elementos\n{ 4 } Custo de Chakra\n{ 5 } Poder de Ataque\n{ 6 } Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha: ");
        opcao = VereficaQuantia(1, 6);

        switch (opcao)
        {
        case 1:
            printf("Novo nome: ");
            VerificadorDeTexto(StrAux);
            free(jutsu->Nome);
            jutsu->Nome = malloc(strlen(StrAux) + 1);
            strcpy(jutsu->Nome, StrAux);
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNome alterado com sucesso!\n");
            break;

        case 2:
            printf("Novo tipo: ");
            VerificadorDeTexto(StrAux);
            free(jutsu->TipoJutsu);
            jutsu->TipoJutsu = malloc(strlen(StrAux) + 1);
            strcpy(jutsu->TipoJutsu, StrAux);
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nTipo alterado com sucesso!\n");
            break;

        case 3:
            if (jutsu->QuantidadeDeElementos > 0 && jutsu->Elementos != NULL)
            {
                printf("\nElementos atuais:\n");
                for (int i = 0; i < jutsu->QuantidadeDeElementos; i++)
                {
                    if (jutsu->Elementos[i] != NULL)
                    {
                        printf("{ %d } %s\n", i + 1, jutsu->Elementos[i]);
                    }
                }

                printf("Digite o número do elemento que deseja alterar: ");
                int elem_idx = VereficaQuantia(1, jutsu->QuantidadeDeElementos) - 1;

                printf("Novo nome do elemento: ");
                VerificadorDeTexto(StrAux);

                // Liberar memória do elemento antigo e alocar nova
                free(jutsu->Elementos[elem_idx]);
                jutsu->Elementos[elem_idx] = malloc(strlen(StrAux) + 1);
                strcpy(jutsu->Elementos[elem_idx], StrAux);

                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nElemento alterado com sucesso!\n");
            }
            else
            {
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEste jutsu não possui elementos para alterar.\n");
            }
            break;

        case 4:
            printf("Novo custo de chakra (1-100): ");
            jutsu->CustodeChakra = VereficaQuantia(1, 100);
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nCusto de chakra alterado com sucesso!\n");
            break;

        case 5:
            printf("Novo poder de ataque (1-100): ");
            jutsu->PoderdeAtaque = VereficaQuantia(1, 100);
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nPoder de ataque alterado com sucesso!\n");
            break;
        }

        if (opcao != 6)
        {
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nDeseja alterar mais algo neste jutsu? (s/n): ");
            if (simOUnao() == 0)
                break;
        }

    } while (opcao != 6);
}

void ExibirDadosAtuais(int TipoDeContexto, int indice)
{
    printf("\nDADOS ATUAIS:\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");

    if (indice < 0)
    {
        printf("Erro: Índice inválido!\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
        return;
    }

    switch (TipoDeContexto)
    {
    case 1: // Shinobi
        if (indice < _NumeroDoShinobi && _ConjuntoDeShinobis != NULL)
        {
            printf("Nome: %s\n", _ConjuntoDeShinobis[indice].Nome);
            if (_ConjuntoDeShinobis[indice].CaracteristicasDoCla && _ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla)
                printf("Clã: %s\n", _ConjuntoDeShinobis[indice].CaracteristicasDoCla->NomeDoCla);
            else
                printf("Clã: Nenhum\n");
            printf("Patente: %s\nChakra: %d\nElemento: %s\n", _ConjuntoDeShinobis[indice].Patente, _ConjuntoDeShinobis[indice].QuantidadeDeChakra, _ConjuntoDeShinobis[indice].ElementoPrincipal);
        }
        break;

    case 2: // Clã
        if (indice < _NumeroDoCla && _ConjuntoDeClas != NULL)
        {
            printf("Nome: %s\nJutsus Hereditários: %d\nJutsus Tradicionais: %d\n", _ConjuntoDeClas[indice].NomeDoCla, _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios, _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais);
        }
        break;

    case 3: // Jutsu
        if (indice < _NumeroDoJutsu && _ConjuntoDeJutsus != NULL)
        {
            printf("Nome: %s\nTipo: %s\nCusto de Chakra: %d\nPoder de Ataque: %d\n", _ConjuntoDeJutsus[indice].Nome, _ConjuntoDeJutsus[indice].TipoJutsu, _ConjuntoDeJutsus[indice].CustodeChakra, _ConjuntoDeJutsus[indice].PoderdeAtaque);
        }
        break;

    case 4: // Missão
        if (indice < _NumeroDaMissao && _ConjuntoDeMissoes != NULL)
        {
            printf("Título: %s\n", _ConjuntoDeMissoes[indice].Titulo);
            if (_ConjuntoDeMissoes[indice].LiderResponsavel && _ConjuntoDeMissoes[indice].LiderResponsavel->Nome)
                printf("Líder: %s\n", _ConjuntoDeMissoes[indice].LiderResponsavel->Nome);
            else
                printf("Líder: Nenhum\n");
            printf("Nível: %s\nStatus: %s\nQuantidade de Ninjas: %d\n", _ConjuntoDeMissoes[indice].NivelDificuldade, _ConjuntoDeMissoes[indice].Status, _ConjuntoDeMissoes[indice].QuantidadeDeNinjas);
        }
        break;
    }
    printf("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
}

void AlterarMissaoEspecifica(tMissao *missao)
{
    int opcao;
    char StrAux[100];

    do
    {
        limpar_tela();
        printf("\n<~==[ ALTERAR MISSAO: %s ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n", missao->Titulo);

        // MOSTRA DADOS ATUAIS SEMPRE
        printf("DADOS ATUAIS:\n- Título: %s\n- Líder: %s (%s)\n- Nível: %s\n- Status: %s\n- Quantidade de Ninjas: %d\n- Ninjas: ", missao->Titulo, missao->LiderResponsavel->Nome, missao->LiderResponsavel->Patente, missao->NivelDificuldade, missao->Status, missao->QuantidadeDeNinjas);
        if (missao->QuantidadeDeNinjas > 0 && missao->NinjasDaMissao != NULL)
        {
            for (int i = 0; i < missao->QuantidadeDeNinjas; i++)
            {
                if (missao->NinjasDaMissao[i] != NULL && missao->NinjasDaMissao[i]->Nome != NULL)
                {
                    printf("%s (%s)", missao->NinjasDaMissao[i]->Nome, missao->NinjasDaMissao[i]->Patente);
                    if (i < missao->QuantidadeDeNinjas - 1)
                        printf(", ");
                }
            }
        }
        else
        {
            printf("Nenhum");
        }
        printf("\n");

        printf("\nO QUE DESEJA ALTERAR?\n{ 1 } Título\n{ 2 } Líder\n{ 3 } Nível\n{ 4 } Status\n{ 5 } Ninjas\n{ 6 } Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha: ");
        opcao = VereficaQuantia(1, 6);

        switch (opcao)
        {
        case 1: // Título
            printf("\n<~==[ ALTERAR: TITULO DA MISSAO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nTítulo atual: %s\nNovo título: ", missao->Titulo);
            VerificadorDeTexto(StrAux);
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nConfirmar alteração? (s/n): ");
            if (simOUnao() == 1)
            {
                free(missao->Titulo);
                missao->Titulo = malloc(strlen(StrAux) + 1);
                strcpy(missao->Titulo, StrAux);
                printf("Título alterado para: %s\n", missao->Titulo);
            }
            else
            {
                printf("Alteração cancelada!\n");
            }
            break;

        case 2: // Líder
            printf("\n<~==[ ALTERAR: LIDER DA MISSAO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nLíder atual: %s (%s)\nNível atual da missão: %s\nSelecionando novo líder...\n", missao->LiderResponsavel->Nome, missao->LiderResponsavel->Patente, missao->NivelDificuldade);
            congelar_tela();

            int idxLider = EscolheOpçao(1) - 1;
            if (idxLider >= 0)
            {
                char *novaPatente = _ConjuntoDeShinobis[idxLider].Patente;
                char *nivelAtual = missao->NivelDificuldade;
                int liderValido = 1;

                // VERIFICAÇÕES DO LÍDER
                if (strcmp(novaPatente, "Estudante da Academia") == 0)
                {
                    printf("ERRO: Estudante não pode liderar missões!\n");
                    liderValido = 0;
                }
                else if (strcmp(novaPatente, "Genin") == 0)
                {
                    printf("ERRO: Genin não tem autoridade para liderar!\n");
                    liderValido = 0;
                }
                else if (strcmp(novaPatente, "Chunin") == 0 && (strcmp(nivelAtual, "A") == 0 || strcmp(nivelAtual, "S") == 0))
                {
                    printf("ERRO: Chunin só pode liderar missões nível C ou B!\n");
                    liderValido = 0;
                }

                if (liderValido)
                {
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nConfirmar alteração para %s (%s)? (s/n): ", _ConjuntoDeShinobis[idxLider].Nome, novaPatente);
                    if (simOUnao() == 1)
                    {
                        free(missao->LiderResponsavel->Nome);
                        missao->LiderResponsavel->Nome = malloc(strlen(_ConjuntoDeShinobis[idxLider].Nome) + 1);
                        strcpy(missao->LiderResponsavel->Nome, _ConjuntoDeShinobis[idxLider].Nome);

                        free(missao->LiderResponsavel->Patente);
                        missao->LiderResponsavel->Patente = malloc(strlen(novaPatente) + 1);
                        strcpy(missao->LiderResponsavel->Patente, novaPatente);

                        printf("Líder alterado para: %s (%s)\n", missao->LiderResponsavel->Nome, missao->LiderResponsavel->Patente);
                    }
                    else
                    {
                        printf("Alteração cancelada!\n");
                    }
                }
                else
                {
                    printf("Selecione um líder com patente adequada!\n");
                }
            }
            congelar_tela();
            break;

        case 3: // Nível
            printf("\n<~==[ ALTERAR: NIVEL DA MISSAO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNível atual: %s\nLíder atual: %s (%s)\nNovo nível (D/C/B/A/S): ", missao->NivelDificuldade, missao->LiderResponsavel->Nome, missao->LiderResponsavel->Patente);
            VerificaNivelDeDificuldade(StrAux);

            // VERIFICA SE LÍDER ATUAL É COMPATÍVEL COM NOVO NÍVEL
            int nivelValido = 1;
            char *patenteLider = missao->LiderResponsavel->Patente;

            if (strcmp(patenteLider, "Chunin") == 0 && (strcmp(StrAux, "A") == 0 || strcmp(StrAux, "S") == 0))
            {
                printf("ERRO: Líder atual (%s) não pode liderar missão nível %s!\nChunin só pode liderar missões nível C ou B.\n", patenteLider, StrAux);
                nivelValido = 0;
            }
            else if (strcmp(patenteLider, "Estudante da Academia") == 0 || strcmp(patenteLider, "Genin") == 0)
            {
                printf("ERRO: Líder atual (%s) não pode liderar missões!\n", patenteLider);
                nivelValido = 0;
            }

            if (nivelValido)
            {
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nConfirmar alteração para %s? (s/n): ", StrAux);
                if (simOUnao() == 1)
                {
                    free(missao->NivelDificuldade);
                    missao->NivelDificuldade = malloc(strlen(StrAux) + 1);
                    strcpy(missao->NivelDificuldade, StrAux);
                    printf("Nível alterado para: %s\n", missao->NivelDificuldade);
                }
                else
                {
                    printf("Alteração cancelada!\n");
                }
            }
            else
            {
                printf("Altere o líder primeiro ou escolha outro nível!\n");
            }
            congelar_tela();
            break;

        case 4: // Status
            printf("\n<~==[ ALTERAR: STATUS DA MISSAO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nStatus atual: %s\nNovo status:\n{ 1 } Pendente\n{ 2 } Em Andamento\n{ 3 } Concluída\n{ 4 } Falha\n{ 5 } Cancelada\nEscolha: ", missao->Status);
            int statusOpcao = VereficaQuantia(1, 5);
            SelecionaStatus(statusOpcao, StrAux);
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nConfirmar alteração para %s? (s/n): ", StrAux);
            if (simOUnao() == 1)
            {
                free(missao->Status);
                missao->Status = malloc(strlen(StrAux) + 1);
                strcpy(missao->Status, StrAux);
                printf("✓ Status alterado para: %s\n", missao->Status);
            }
            else
            {
                printf("Alteração cancelada!\n");
            }
            break;

        case 5: // Ninjas
            printf("\n<~==[ ALTERAR: NINJAS DA MISSAO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");

            if (missao->QuantidadeDeNinjas == 0)
            {
                printf("Esta missão não tem ninjas para alterar.\n");
                congelar_tela();
                break;
            }

            printf("Ninjas atuais:\n");
            for (int i = 0; i < missao->QuantidadeDeNinjas; i++)
            {
                printf("{ %d } %s (%s)\n", i + 1, missao->NinjasDaMissao[i]->Nome, missao->NinjasDaMissao[i]->Patente);
            }

            printf("\nQual ninja substituir? (0 para voltar): ");
            int idxSubstituir = VereficaQuantia(0, missao->QuantidadeDeNinjas) - 1;

            if (idxSubstituir == -1)
            {
                printf("Operação cancelada!\n");
                break;
            }

            printf("Substituindo: %s (%s)\nLíder: %s (%s)\nNível: %s\nSelecionando substituto...\n", missao->NinjasDaMissao[idxSubstituir]->Nome, missao->NinjasDaMissao[idxSubstituir]->Patente, missao->LiderResponsavel->Nome, missao->LiderResponsavel->Patente, missao->NivelDificuldade);
            congelar_tela();

            int idxNovo = EscolheOpçao(1) - 1;
            if (idxNovo >= 0)
            {
                char *patenteNovo = _ConjuntoDeShinobis[idxNovo].Patente;
                char *patenteLider = missao->LiderResponsavel->Patente;
                char *nivelMissao = missao->NivelDificuldade;
                int ninjaValido = 1;

                // VERIFICAÇÕES DO NINJA
                if (strcmp(patenteNovo, "Estudante da Academia") == 0)
                {
                    printf("ERRO: Estudante não pode participar de missões!\n");
                    ninjaValido = 0;
                }
                else if (strcmp(patenteNovo, "Genin") == 0)
                {
                    if (strcmp(nivelMissao, "D") != 0 && strcmp(nivelMissao, "C") != 0)
                    {
                        printf("ERRO: Genin só pode em missões nível D ou C!\n");
                        ninjaValido = 0;
                    }
                    else if (strcmp(patenteLider, "Chunin") != 0 && strcmp(patenteLider, "Jounin") != 0 && strcmp(patenteLider, "Tokubetsu Jounin") != 0 && strcmp(patenteLider, "Kage") != 0)
                    {
                        printf("ERRO: Genin precisa de líder Chunin ou superior!\n");
                        ninjaValido = 0;
                    }
                }
                else if (strcmp(patenteNovo, "Chunin") == 0 && strcmp(nivelMissao, "S") == 0)
                {
                    printf("ERRO: Chunin não pode em missões nível S!\n");
                    ninjaValido = 0;
                }
                else if ((strcmp(patenteNovo, "Jounin") == 0 || strcmp(patenteNovo, "Tokubetsu Jounin") == 0))
                {
                    if (strcmp(patenteLider, "Chunin") == 0)
                    {
                        printf("ERRO: Jounin não segue ordens de Chunin!\n");
                        ninjaValido = 0;
                    }
                    else if (strcmp(nivelMissao, "S") == 0)
                    {
                        printf("AVISO: Jounin só em missão S com ordem do Hokage!\n");
                        // Deixa passar com aviso
                    }
                }
                else if (strcmp(patenteNovo, "ANBU") == 0)
                {
                    if (strcmp(nivelMissao, "A") != 0 && strcmp(nivelMissao, "S") != 0)
                    {
                        printf("ERRO: ANBU só atua em missões nível A ou S!\n");
                        ninjaValido = 0;
                    }
                    else if (strcmp(patenteLider, "Kage") != 0)
                    {
                        printf("AVISO: ANBU normalmente age sob ordens diretas do Kage!\n");
                        // Deixa passar com aviso
                    }
                }

                if (ninjaValido)
                {
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nConfirmar substituição de %s por %s (%s)? (s/n): ", missao->NinjasDaMissao[idxSubstituir]->Nome, _ConjuntoDeShinobis[idxNovo].Nome, patenteNovo);
                    if (simOUnao() == 1)
                    {
                        free(missao->NinjasDaMissao[idxSubstituir]->Nome);
                        missao->NinjasDaMissao[idxSubstituir]->Nome = malloc(strlen(_ConjuntoDeShinobis[idxNovo].Nome) + 1);
                        strcpy(missao->NinjasDaMissao[idxSubstituir]->Nome, _ConjuntoDeShinobis[idxNovo].Nome);

                        free(missao->NinjasDaMissao[idxSubstituir]->Patente);
                        missao->NinjasDaMissao[idxSubstituir]->Patente = malloc(strlen(patenteNovo) + 1);
                        strcpy(missao->NinjasDaMissao[idxSubstituir]->Patente, patenteNovo);

                        printf("✓ Ninja substituído!\n");
                    }
                    else
                    {
                        printf("Substituição cancelada!\n");
                    }
                }
                else
                {
                    printf("Selecione um ninja com patente adequada para esta missão!\n");
                }
            }
            congelar_tela();
            break;

        case 6: // Voltar
            printf("Voltando ao menu anterior...\n");
            break;
        }

        if (opcao != 6)
        {
            printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nDeseja alterar mais algo nesta missão? (s/n): ");
            if (simOUnao() == 0)
                break;
        }

    } while (opcao != 6);
}

void Remover(int TipoDeContexto)
{
    int opcaoPrincipal, indice;
    int limite;
    char *tipoNome;

    while (1)
    {
        limpar_tela();
        printf("\n<~==[ REMOVER REGISTRO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");

        // Determinar o limite e nome baseado no tipo de contexto
        switch (TipoDeContexto)
        {
        case 1: // Shinobi
            if (_NumeroDoShinobi == 0)
            {
                printf("\nNenhum shinobi cadastrado!\n");
                congelar_tela();
                return;
            }
            printf("Shinobis cadastrados:\n");
            for (int i = 0; i < _NumeroDoShinobi; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeShinobis[i].Nome);
            }
            limite = _NumeroDoShinobi;
            tipoNome = "shinobi";
            break;

        case 2: // Clã
            if (_NumeroDoCla == 0)
            {
                printf("\nNenhum clã cadastrado!\n");
                congelar_tela();
                return;
            }
            printf("Clãs cadastrados:\n");
            for (int i = 0; i < _NumeroDoCla; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeClas[i].NomeDoCla);
            }
            limite = _NumeroDoCla;
            tipoNome = "clã";
            break;

        case 3: // Jutsu
            if (_NumeroDoJutsu == 0)
            {
                printf("\nNenhum jutsu cadastrado!\n");
                congelar_tela();
                return;
            }
            printf("Jutsus cadastrados:\n");
            for (int i = 0; i < _NumeroDoJutsu; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeJutsus[i].Nome);
            }
            limite = _NumeroDoJutsu;
            tipoNome = "jutsu";
            break;

        case 4: // Missão
            if (_NumeroDaMissao == 0)
            {
                printf("\nNenhuma missão cadastrada!\n");
                congelar_tela();
                return;
            }
            printf("Missões cadastradas:\n");
            for (int i = 0; i < _NumeroDaMissao; i++)
            {
                printf("{ %d } - %s\n", i + 1, _ConjuntoDeMissoes[i].Titulo);
            }
            limite = _NumeroDaMissao;
            tipoNome = "missão";
            break;

        default:
            printf("\nTipo de contexto inválido!\n");
            congelar_tela();
            return;
        }

        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nSelecione o %s:\n{ 0 } - Voltar ao Menu Anterior\nOpção: ", tipoNome);
        indice = VereficaQuantia(0, limite);

        if (indice == 0)
        {
            limpar_tela();
            return;
        }

        // ESCOLHA DO TIPO DE REMOÇÃO
        limpar_tela();
        printf("\n<~==[ TIPO DE REMOÇÃO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRegistro selecionado:\n\n");
        RelatorioDaOpçao(TipoDeContexto, indice, 1);

        printf("\nComo deseja proceder?\n{ 1 } - Remover %s por completo\n{ 2 } - Remover atributos específicos\n{ 3 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ", tipoNome);
        opcaoPrincipal = VereficaQuantia(1, 3);

        if (opcaoPrincipal == 3)
        {
            continue; // Volta para a lista de registros
        }

        if (opcaoPrincipal == 1)
        {
            // REMOÇÃO COMPLETA
            printf("\n<~==[ CONFIRMAR REMOÇÃO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nTem certeza que deseja remover este %s por completo? (s/n): ", tipoNome);

            if (simOUnao() == 1)
            {
                printf("\n<~==[ CONFIRMAÇÃO FINAL ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nESTA AÇÃO É IRREVERSÍVEL!\nDeseja realmente prosseguir? (s/n): ");

                if (simOUnao() == 1)
                {
                    // LÓGICA DE REMOÇÃO COMPLETA
                    int i;
                    switch (TipoDeContexto)
                    {
                    case 1: // Remover Shinobi
                        if (_ConjuntoDeShinobis[indice - 1].Nome != NULL)
                            free(_ConjuntoDeShinobis[indice - 1].Nome);
                        if (_ConjuntoDeShinobis[indice - 1].Patente != NULL)
                            free(_ConjuntoDeShinobis[indice - 1].Patente);
                        if (_ConjuntoDeShinobis[indice - 1].ElementoPrincipal != NULL)
                            free(_ConjuntoDeShinobis[indice - 1].ElementoPrincipal);

                        for (i = indice - 1; i < _NumeroDoShinobi - 1; i++)
                        {
                            _ConjuntoDeShinobis[i] = _ConjuntoDeShinobis[i + 1];
                        }
                        _NumeroDoShinobi--;
                        _ConjuntoDeShinobis = (tNinja *)realloc(_ConjuntoDeShinobis, _NumeroDoShinobi * sizeof(tNinja));
                        break;

                    case 2: // Remover Clã
                        if (_ConjuntoDeClas[indice - 1].NomeDoCla != NULL)
                            free(_ConjuntoDeClas[indice - 1].NomeDoCla);

                        for (i = indice - 1; i < _NumeroDoCla - 1; i++)
                        {
                            _ConjuntoDeClas[i] = _ConjuntoDeClas[i + 1];
                        }
                        _NumeroDoCla--;
                        _ConjuntoDeClas = (tCla *)realloc(_ConjuntoDeClas, _NumeroDoCla * sizeof(tCla));
                        break;

                    case 3: // Remover Jutsu
                        if (_ConjuntoDeJutsus[indice - 1].Nome != NULL)
                            free(_ConjuntoDeJutsus[indice - 1].Nome);
                        if (_ConjuntoDeJutsus[indice - 1].TipoJutsu != NULL)
                            free(_ConjuntoDeJutsus[indice - 1].TipoJutsu);

                        for (i = indice - 1; i < _NumeroDoJutsu - 1; i++)
                        {
                            _ConjuntoDeJutsus[i] = _ConjuntoDeJutsus[i + 1];
                        }
                        _NumeroDoJutsu--;
                        _ConjuntoDeJutsus = (tJutsu *)realloc(_ConjuntoDeJutsus, _NumeroDoJutsu * sizeof(tJutsu));
                        break;

                    case 4: // Remover Missão
                        if (_ConjuntoDeMissoes[indice - 1].Titulo != NULL)
                            free(_ConjuntoDeMissoes[indice - 1].Titulo);
                        if (_ConjuntoDeMissoes[indice - 1].NivelDificuldade != NULL)
                            free(_ConjuntoDeMissoes[indice - 1].NivelDificuldade);
                        if (_ConjuntoDeMissoes[indice - 1].Status != NULL)
                            free(_ConjuntoDeMissoes[indice - 1].Status);

                        for (i = indice - 1; i < _NumeroDaMissao - 1; i++)
                        {
                            _ConjuntoDeMissoes[i] = _ConjuntoDeMissoes[i + 1];
                        }
                        _NumeroDaMissao--;
                        _ConjuntoDeMissoes = (tMissao *)realloc(_ConjuntoDeMissoes, _NumeroDaMissao * sizeof(tMissao));
                        break;
                    }

                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n%s removido com sucesso!\n", tipoNome);
                    congelar_tela();
                }
                else
                {
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemoção cancelada!\n");
                    congelar_tela();
                }
            }
            else
            {
                printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemoção cancelada!\n");
                congelar_tela();
            }
        }
        else if (opcaoPrincipal == 2)
        {
            // REMOVER ATRIBUTOS ESPECÍFICOS
            RemoverAtributosEspecificos(TipoDeContexto, indice - 1);
        }

        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nDeseja trabalhar em outro %s? (s/n): ", tipoNome);
        if (simOUnao() == 0)
        {
            limpar_tela();
            break;
        }
    }
}

void RemoverAtributosEspecificos(int TipoDeContexto, int indice)
{
    int opcaoAtributo, opcaoItem;

    do
    {
        limpar_tela();
        printf("\n<~==[ REMOVER ATRIBUTOS ESPECÍFICOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");

        // Mostrar dados atuais
        ExibirDadosAtuais(TipoDeContexto, indice);

        printf("\nO que deseja remover?\n");

        switch (TipoDeContexto)
        {
        case 1: // Shinobi
            // Jutsus Dominados
            if (_ConjuntoDeShinobis[indice].QuantidadeDeJutsus > 0 && _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu != NULL)
            {
                printf("{ 1 } - Jutsus Dominados\n{ 2 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ");
                opcaoAtributo = VereficaQuantia(1, 2);

                if (opcaoAtributo == 1)
                {
                    printf("\n<~==[ REMOVER: JUTSUS DOMINADOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsus dominados atuais:\n\n");

                    for (int i = 0; i < _ConjuntoDeShinobis[indice].QuantidadeDeJutsus; i++)
                    {
                        if (_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[i] != NULL && _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[i]->Nome != NULL)
                        {
                            printf("{ %d } - %s\n", i + 1, _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[i]->Nome);
                        }
                    }

                    printf("\nDigite o número do jutsu a remover (0 para voltar): ");
                    opcaoItem = VereficaQuantia(0, _ConjuntoDeShinobis[indice].QuantidadeDeJutsus);

                    if (opcaoItem == 0)
                        break;

                    int idx = opcaoItem - 1;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemover jutsu '%s'? (s/n): ", _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->Nome);

                    if (simOUnao() == 1)
                    {
                        // Liberar memória do jutsu específico
                        if (_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->Nome != NULL)
                            free(_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->Nome);
                        if (_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->TipoJutsu != NULL)
                            free(_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->TipoJutsu);

                        // Liberar elementos se existirem
                        if (_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->QuantidadeDeElementos > 0 && _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->Elementos != NULL)
                        {
                            for (int j = 0; j < _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->QuantidadeDeElementos; j++)
                            {
                                if (_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->Elementos[j] != NULL)
                                    free(_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->Elementos[j]);
                            }
                            free(_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]->Elementos);
                        }

                        free(_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[idx]);

                        // Reorganizar vetor
                        for (int i = idx; i < _ConjuntoDeShinobis[indice].QuantidadeDeJutsus - 1; i++)
                        {
                            _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[i] = _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu[i + 1];
                        }

                        _ConjuntoDeShinobis[indice].QuantidadeDeJutsus--;

                        // Realocar memória se necessário
                        if (_ConjuntoDeShinobis[indice].QuantidadeDeJutsus > 0)
                        {
                            _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu = (tJutsu **)realloc(_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu, _ConjuntoDeShinobis[indice].QuantidadeDeJutsus * sizeof(tJutsu *));
                        }
                        else
                        {
                            free(_ConjuntoDeShinobis[indice].CaracteristicasDoJutsu);
                            _ConjuntoDeShinobis[indice].CaracteristicasDoJutsu = NULL;
                        }

                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsu removido com sucesso!\n");
                    }
                    else
                    {
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemoção cancelada!\n");
                    }
                }
            }
            else
            {
                printf("\n<~==[ REMOVER ATRIBUTOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEste shinobi não possui atributos com múltiplos valores para remover.\n(Jutsus Dominados)\n{ 1 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ");
                VereficaQuantia(1, 1);
            }
            break;

        case 2: // Clã
            printf("{ 1 } - Jutsus Hereditarios\n{ 2 } - Jutsus Tradicionais\n{ 3 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ");
            opcaoAtributo = VereficaQuantia(1, 3);

            switch (opcaoAtributo)
            {
            case 1: // Remover Jutsus Hereditários
                if (_ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios > 0 && _ConjuntoDeClas[indice].JutsusHereditarios != NULL)
                {
                    printf("\n<~==[ REMOVER: JUTSUS HEREDITARIOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsus hereditários atuais:\n\n");

                    for (int i = 0; i < _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios; i++)
                    {
                        if (_ConjuntoDeClas[indice].JutsusHereditarios[i] != NULL && _ConjuntoDeClas[indice].JutsusHereditarios[i]->Nome != NULL)
                        {
                            printf("{ %d } - %s\n", i + 1, _ConjuntoDeClas[indice].JutsusHereditarios[i]->Nome);
                        }
                    }

                    printf("\nDigite o número do jutsu a remover (0 para voltar): ");
                    opcaoItem = VereficaQuantia(0, _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios);

                    if (opcaoItem == 0)
                    {
                        break;
                    }

                    int idx = opcaoItem - 1;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemover jutsu hereditário '%s'? (s/n): ", _ConjuntoDeClas[indice].JutsusHereditarios[idx]->Nome);

                    if (simOUnao() == 1)
                    {
                        // Liberar memória do jutsu específico
                        if (_ConjuntoDeClas[indice].JutsusHereditarios[idx]->Nome != NULL)
                            free(_ConjuntoDeClas[indice].JutsusHereditarios[idx]->Nome);
                        if (_ConjuntoDeClas[indice].JutsusHereditarios[idx]->TipoJutsu != NULL)
                            free(_ConjuntoDeClas[indice].JutsusHereditarios[idx]->TipoJutsu);

                        // Liberar elementos se existirem
                        if (_ConjuntoDeClas[indice].JutsusHereditarios[idx]->QuantidadeDeElementos > 0 && _ConjuntoDeClas[indice].JutsusHereditarios[idx]->Elementos != NULL)
                        {
                            for (int j = 0; j < _ConjuntoDeClas[indice].JutsusHereditarios[idx]->QuantidadeDeElementos; j++)
                            {
                                if (_ConjuntoDeClas[indice].JutsusHereditarios[idx]->Elementos[j] != NULL)
                                    free(_ConjuntoDeClas[indice].JutsusHereditarios[idx]->Elementos[j]);
                            }
                            free(_ConjuntoDeClas[indice].JutsusHereditarios[idx]->Elementos);
                        }

                        free(_ConjuntoDeClas[indice].JutsusHereditarios[idx]);

                        // Reorganizar array
                        for (int i = idx; i < _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios - 1; i++)
                        {
                            _ConjuntoDeClas[indice].JutsusHereditarios[i] = _ConjuntoDeClas[indice].JutsusHereditarios[i + 1];
                        }

                        _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios--;

                        // Realocar memória se necessário
                        if (_ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios > 0)
                        {
                            _ConjuntoDeClas[indice].JutsusHereditarios = (tJutsu **)realloc(_ConjuntoDeClas[indice].JutsusHereditarios, _ConjuntoDeClas[indice].QuantidadeDeJutsusHereditarios * sizeof(tJutsu *));
                        }
                        else
                        {
                            free(_ConjuntoDeClas[indice].JutsusHereditarios);
                            _ConjuntoDeClas[indice].JutsusHereditarios = NULL;
                        }

                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsu hereditário removido com sucesso!\n");
                    }
                    else
                    {
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemoção cancelada!\n");
                    }
                }
                else
                {
                    printf("\n<~==[ REMOVER: JUTSUS HEREDITARIOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEste clã não possui jutsus hereditários para remover.\n");
                }
                break;

            case 2: // Remover Jutsus Tradicionais
                if (_ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais > 0 && _ConjuntoDeClas[indice].JutsusTradicionais != NULL)
                {
                    printf("\n<~==[ REMOVER: JUTSUS TRADICIONAIS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsus tradicionais atuais:\n\n");

                    for (int i = 0; i < _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais; i++)
                    {
                        if (_ConjuntoDeClas[indice].JutsusTradicionais[i] != NULL && _ConjuntoDeClas[indice].JutsusTradicionais[i]->Nome != NULL)
                        {
                            printf("{ %d } - %s\n", i + 1, _ConjuntoDeClas[indice].JutsusTradicionais[i]->Nome);
                        }
                    }

                    printf("\nDigite o número do jutsu a remover (0 para voltar): ");
                    opcaoItem = VereficaQuantia(0, _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais);

                    if (opcaoItem == 0)
                    {
                        break;
                    }

                    int idx = opcaoItem - 1;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemover jutsu tradicional '%s'? (s/n): ", _ConjuntoDeClas[indice].JutsusTradicionais[idx]->Nome);

                    if (simOUnao() == 1)
                    {
                        // Liberar memória do jutsu específico
                        if (_ConjuntoDeClas[indice].JutsusTradicionais[idx]->Nome != NULL)
                            free(_ConjuntoDeClas[indice].JutsusTradicionais[idx]->Nome);
                        if (_ConjuntoDeClas[indice].JutsusTradicionais[idx]->TipoJutsu != NULL)
                            free(_ConjuntoDeClas[indice].JutsusTradicionais[idx]->TipoJutsu);

                        // Liberar elementos se existirem
                        if (_ConjuntoDeClas[indice].JutsusTradicionais[idx]->QuantidadeDeElementos > 0 && _ConjuntoDeClas[indice].JutsusTradicionais[idx]->Elementos != NULL)
                        {
                            for (int j = 0; j < _ConjuntoDeClas[indice].JutsusTradicionais[idx]->QuantidadeDeElementos; j++)
                            {
                                if (_ConjuntoDeClas[indice].JutsusTradicionais[idx]->Elementos[j] != NULL)
                                    free(_ConjuntoDeClas[indice].JutsusTradicionais[idx]->Elementos[j]);
                            }
                            free(_ConjuntoDeClas[indice].JutsusTradicionais[idx]->Elementos);
                        }

                        free(_ConjuntoDeClas[indice].JutsusTradicionais[idx]);

                        // Reorganizar vetor
                        for (int i = idx; i < _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais - 1; i++)
                        {
                            _ConjuntoDeClas[indice].JutsusTradicionais[i] = _ConjuntoDeClas[indice].JutsusTradicionais[i + 1];
                        }

                        _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais--;

                        // Realocar memória se necessário
                        if (_ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais > 0)
                        {
                            _ConjuntoDeClas[indice].JutsusTradicionais = (tJutsu **)realloc(_ConjuntoDeClas[indice].JutsusTradicionais, _ConjuntoDeClas[indice].QuantidadeDeJutsusTradicionais * sizeof(tJutsu *));
                        }
                        else
                        {
                            free(_ConjuntoDeClas[indice].JutsusTradicionais);
                            _ConjuntoDeClas[indice].JutsusTradicionais = NULL;
                        }

                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nJutsu tradicional removido com sucesso!\n");
                    }
                    else
                    {
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemoção cancelada!\n");
                    }
                }
                else
                {
                    printf("\n<~==[ REMOVER: JUTSUS TRADICIONAIS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEste clã não possui jutsus tradicionais para remover.\n");
                }
                break;
            }
            break;

        case 3: // Jutsu
            if (_ConjuntoDeJutsus[indice].QuantidadeDeElementos > 0 && _ConjuntoDeJutsus[indice].Elementos != NULL)
            {
                printf("{ 1 } - Elementos\n{ 2 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ");
                opcaoAtributo = VereficaQuantia(1, 2);

                if (opcaoAtributo == 1)
                {
                    printf("\n<~==[ REMOVER: ELEMENTOS DO JUTSU ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nElementos atuais:\n\n");

                    for (int i = 0; i < _ConjuntoDeJutsus[indice].QuantidadeDeElementos; i++)
                    {
                        if (_ConjuntoDeJutsus[indice].Elementos[i] != NULL)
                        {
                            printf("{ %d } - %s\n", i + 1, _ConjuntoDeJutsus[indice].Elementos[i]);
                        }
                    }

                    printf("\nDigite o número do elemento a remover (0 para voltar): ");
                    opcaoItem = VereficaQuantia(0, _ConjuntoDeJutsus[indice].QuantidadeDeElementos);

                    if (opcaoItem == 0)
                        break;

                    int idx = opcaoItem - 1;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemover elemento '%s'? (s/n): ", _ConjuntoDeJutsus[indice].Elementos[idx]);

                    if (simOUnao() == 1)
                    {
                        free(_ConjuntoDeJutsus[indice].Elementos[idx]);

                        // Reorganizar array
                        for (int i = idx; i < _ConjuntoDeJutsus[indice].QuantidadeDeElementos - 1; i++)
                        {
                            _ConjuntoDeJutsus[indice].Elementos[i] = _ConjuntoDeJutsus[indice].Elementos[i + 1];
                        }

                        _ConjuntoDeJutsus[indice].QuantidadeDeElementos--;

                        // Realocar memória se necessário
                        if (_ConjuntoDeJutsus[indice].QuantidadeDeElementos > 0)
                        {
                            _ConjuntoDeJutsus[indice].Elementos = (char **)realloc(_ConjuntoDeJutsus[indice].Elementos, _ConjuntoDeJutsus[indice].QuantidadeDeElementos * sizeof(char *));
                        }
                        else
                        {
                            free(_ConjuntoDeJutsus[indice].Elementos);
                            _ConjuntoDeJutsus[indice].Elementos = NULL;
                        }

                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nElemento removido com sucesso!\n");
                    }
                    else
                    {
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemoção cancelada!\n");
                    }
                }
            }
            else
            {
                printf("\n<~==[ REMOVER ATRIBUTOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEste jutsu não possui atributos com múltiplos valores para remover.\n(Elementos)\n{ 1 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ");
                VereficaQuantia(1, 1);
            }
            break;

        case 4: // Missão
            // SÓ Ninjas (pode ter múltiplos)
            if (_ConjuntoDeMissoes[indice].QuantidadeDeNinjas > 0 && _ConjuntoDeMissoes[indice].NinjasDaMissao != NULL)
            {
                printf("{ 1 } - Ninjas da Missao\n{ 2 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ");
                opcaoAtributo = VereficaQuantia(1, 2);

                if (opcaoAtributo == 1)
                {
                    printf("\n<~==[ REMOVER: NINJAS DA MISSAO ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNinjas atuais na missão:\n\n");

                    for (int i = 0; i < _ConjuntoDeMissoes[indice].QuantidadeDeNinjas; i++)
                    {
                        if (_ConjuntoDeMissoes[indice].NinjasDaMissao[i] != NULL && _ConjuntoDeMissoes[indice].NinjasDaMissao[i]->Nome != NULL)
                        {
                            printf("{ %d } - %s (%s)\n", i + 1, _ConjuntoDeMissoes[indice].NinjasDaMissao[i]->Nome, _ConjuntoDeMissoes[indice].NinjasDaMissao[i]->Patente);
                        }
                    }

                    printf("\nDigite o número do ninja a remover (0 para voltar): ");
                    opcaoItem = VereficaQuantia(0, _ConjuntoDeMissoes[indice].QuantidadeDeNinjas);

                    if (opcaoItem == 0)
                        break;

                    int idx = opcaoItem - 1;
                    printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemover ninja '%s' da missão? (s/n): ", _ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->Nome);

                    if (simOUnao() == 1)
                    {
                        // Liberar memória do ninja específico
                        if (_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->Nome != NULL)
                            free(_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->Nome);
                        if (_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->Patente != NULL)
                            free(_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->Patente);
                        if (_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->ElementoPrincipal != NULL)
                            free(_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->ElementoPrincipal);

                        // Liberar clã se existir
                        if (_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->CaracteristicasDoCla != NULL)
                        {
                            if (_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->CaracteristicasDoCla->NomeDoCla != NULL)
                                free(_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->CaracteristicasDoCla->NomeDoCla);
                            free(_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]->CaracteristicasDoCla);
                        }

                        free(_ConjuntoDeMissoes[indice].NinjasDaMissao[idx]);

                        // Reorganizar array
                        for (int i = idx; i < _ConjuntoDeMissoes[indice].QuantidadeDeNinjas - 1; i++)
                        {
                            _ConjuntoDeMissoes[indice].NinjasDaMissao[i] = _ConjuntoDeMissoes[indice].NinjasDaMissao[i + 1];
                        }

                        _ConjuntoDeMissoes[indice].QuantidadeDeNinjas--;

                        // Realocar memória se necessário
                        if (_ConjuntoDeMissoes[indice].QuantidadeDeNinjas > 0)
                        {
                            _ConjuntoDeMissoes[indice].NinjasDaMissao = (tNinja **)realloc(_ConjuntoDeMissoes[indice].NinjasDaMissao, _ConjuntoDeMissoes[indice].QuantidadeDeNinjas * sizeof(tNinja *));
                        }
                        else
                        {
                            free(_ConjuntoDeMissoes[indice].NinjasDaMissao);
                            _ConjuntoDeMissoes[indice].NinjasDaMissao = NULL;
                        }

                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nNinja removido da missão com sucesso!\n");
                    }
                    else
                    {
                        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nRemoção cancelada!\n");
                    }
                }
            }
            else
            {
                printf("\n<~==[ REMOVER ATRIBUTOS ]==~>\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEsta missão não possui atributos com múltiplos valores para remover.\n(Ninjas da Missão)\n{ 1 } - Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nEscolha uma opção: ");
                VereficaQuantia(1, 1);
            }
            break;
        }

        if (opcaoAtributo == 2 || opcaoAtributo == 3) // Voltar
        {
            break;
        }

        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nDeseja remover outro item? (s/n): ");
        if (simOUnao() == 0)
            break;

    } while (1);
}

int simOUnao()
{
    char resposta[71];
    int i, simOUnao, aux;
    do
    {
        aux = 0;
        gets(resposta);

        for (i = 0; resposta[i] != '\0'; i++)
        {
            resposta[i] = tolower(resposta[i]);
        }

        if ((strcmp(resposta, "s") == 0 || strcmp(resposta, "sim") == 0))
        {
            simOUnao = 1;
        } // sim
        else if ((strcmp(resposta, "n") == 0 || strcmp(resposta, "nao") == 0))
        {
            simOUnao = 0;
        } // nao
        else
        {
            aux = 1;
        } // erro

    } while (aux == 1 && printf("\nErro! Responda Apenas com 'SIM' ou 'NAO'.\nDigite novamente: "));
    printf("\n");
    return simOUnao;
}

void SelecionaPatente(int Opçao, char StrAux[100])
{
    switch (Opçao)
    {
    case 1:
        strcpy(StrAux, "Estudante da Academia");
        break;

    case 2:
        strcpy(StrAux, "Genin");
        break;

    case 3:
        strcpy(StrAux, "Chunin");
        break;

    case 4:
        strcpy(StrAux, "Tokubetsu Jounin");
        break;

    case 5:
        strcpy(StrAux, "Jounin");
        break;

    case 6:
        strcpy(StrAux, "ANBU");
        break;

    case 7:
        strcpy(StrAux, "Kage");
        break;
    }
}

void SelecionaStatus(int Opçao, char StrAux[100])
{
    switch (Opçao)
    {
    case 1:
        strcpy(StrAux, "Pendente");
        break;

    case 2:
        strcpy(StrAux, "Em Andamento");
        break;

    case 3:
        strcpy(StrAux, "Concluida");
        break;

    case 4:
        strcpy(StrAux, "Falha");
        break;

    case 5:
        strcpy(StrAux, "Cancelada");
        break;
    }
}

int VerficaRepetiçaoDeOpçao(int ValordoI, int ValorMaximoDeOpçoes, int *aux, int CodigoDigitado)
{
    int i, sinal = 0;

    for (i = 0; i < ValorMaximoDeOpçoes; i++)
    {
        if (i != ValordoI && aux[i] != 0 && CodigoDigitado == aux[i])
        {
            sinal = 1;
            congelar_tela();
            break;
        }
    }
    return sinal;
}

void VerificaNivelDeDificuldade(char StrAuX[100])
{
    int i, verificar = 1;

    while (verificar)
    {
        gets(StrAuX);

        for (i = 0; StrAuX[i] != '\0'; i++)
        {
            StrAuX[i] = toupper(StrAuX[i]);
        }

        if ((strcmp(StrAuX, "D") != 0) && (strcmp(StrAuX, "C") != 0) && (strcmp(StrAuX, "B") != 0) && (strcmp(StrAuX, "A") != 0) && (strcmp(StrAuX, "S") != 0))
        {
            printf("\nErro! O Rank So Pode Ser: 'D', 'C', 'B', 'A', 'S'.\nDigite Novamnete: ");
            continue;
        }
        else
        {
            break;
        }
    }
}

void VerificadorDeTexto(char Texto[100])
{
    int i, ctt, vereificar = 1;
    while (vereificar)
    {
        ctt = 1;
        gets(Texto);
        fflush(stdin);
        if (strlen(Texto) == 0)
        {
            printf("\nErro! Voce Deve Digitar Algo.\nDigite Novamnete: ");
            continue;
        }
        for (i = 0; Texto[i] != '\0'; i++)
        {
            if (!isalpha(Texto[i]) && Texto[i] != ' ')
            {
                ctt = 0;
                break;
            }
        }
        if (!ctt)
        {
            printf("\nErro! O Rank So Pode Ser: 'D', 'C', 'B', 'A', 'S'.\nDigite Novamnete: ");
            continue;
        }
        else
        {
            break;
        }
    }

    for (i = 0; Texto[i] != '\0'; i++)
    {
        Texto[i] = tolower(Texto[i]);
    }
    for (i = 0; Texto[i] != '\0'; i++)
    {
        if (i == 0 || Texto[i - 1] == ' ')
        {
            Texto[i] = toupper(Texto[i]);
        }
    }
}

int VereficaQuantia(int QuantiaMinima, int QuantiaMaxima)
{
    char StrAux[100];
    int i, vereficar = 1, NumAux, erro;

    while (vereficar)
    {
        erro = 0;
        gets(StrAux);
        fflush(stdin);

        NumAux = atoi(StrAux);
        for (i = 0; StrAux[i] != '\0'; i++)
        {
            if (i == 0 && StrAux[i] == '-')
            {
                continue;
            }

            if (!isdigit(StrAux[i]) && StrAux[i] != ' ')
            {
                erro = 1;
                printf("\nErro! A Resposta Conter Apenas Numerais Unicos.\nDigite Novamente: ");
                break;
            }
        }

        if (erro)
        {
            continue;
        }
        else
        {
            if (NumAux < QuantiaMinima || NumAux > QuantiaMaxima)
            {
                printf("\nErro! Resposta Invalida.\nDigite Novamente: ");
                continue;
            }
        }

        break;
    }
    return NumAux;
}

int EscolheOpçao(int Contexto)
{
    int i, OpçaoDesejada, VariavelDeContexto;
    char TipoDeContexto[100];

    switch (Contexto)
    {
    case 1:
        VariavelDeContexto = _NumeroDoShinobi;
        strcpy(TipoDeContexto, "SHINOBI");
        break;

    case 2:
        VariavelDeContexto = _NumeroDoCla;
        strcpy(TipoDeContexto, "CLA");
        break;

    case 3:
        VariavelDeContexto = _NumeroDoJutsu;
        strcpy(TipoDeContexto, "JUTSU");
        break;

    case 4:
        VariavelDeContexto = _NumeroDaMissao;
        strcpy(TipoDeContexto, "MISSAO");
        break;
    }

    while (1)
    {
        if (VariavelDeContexto > 0)
        {
            limpar_tela();
            printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> %s DESEJADO ]==~>\n\nCaso a Opçao Desejada Esteja Cadastrada, Digite-a:\n", TipoDeContexto);
            Imprime(2, Contexto);
            OpçaoDesejada = NovoCadastro(Contexto, 2);
        }
        else
        {
            limpar_tela();
            printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> %s DESEJADO ]==~>\n\nCaso a Opçao Desejada Esteja Cadastrada, Digite-a:\n", TipoDeContexto);
            OpçaoDesejada = NovoCadastro(Contexto, 1);
            if (OpçaoDesejada == -1)
            {
                limpar_tela();
                printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> %s DESEJADO ]==~>\n\nCadastre um %s para continuar o cadastro!\n", TipoDeContexto, TipoDeContexto);
                congelar_tela();
                i--;
                continue;
            }
            limpar_tela();
            printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> %s  DESEJADO ]==~>\n\nCaso a Opçao Desejada Esteja Cadastrada, Digite-a:\n", TipoDeContexto);
            Imprime(2, Contexto);
            OpçaoDesejada = NovoCadastro(Contexto, 2);
        }

        limpar_tela();
        printf("\n<~==[ CADASTRO POR OPçAO: SELECIONE -> %s  DESEJADO ]==~>\n", TipoDeContexto);
        RelatorioDaOpçao(Contexto, OpçaoDesejada, 1);
        printf("Esta Seria a Opçao Desejada?(s/n) ");
        if (simOUnao() == 1)
        {
            return OpçaoDesejada;
            break;
        }
        else
        {
            printf("\nEntao Selecione Novamente!\n");
            congelar_tela();
            continue;
        }
    }
    return 0;
}

void SelecionadorDeMenu(int TipoDeMenu)
{
    limpar_tela();

    switch (TipoDeMenu)
    {
    case 0:
        printf("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n                MENU              \n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n{ 1 } Cadastrar\n{ 2 } Exibir\n{ 3 } Alterar\n{ 4 } Deletar\n{ 5 } Sair\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
        break;

    case 1:
        printf("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n           MENU CADASTRAR            \n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n{ 1 } Cadastrar Shinobi\n{ 2 } Cadastrar Clã\n{ 3 } Cadastrar Jutsu\n{ 4 } Cadastrar Missão\n{ 5 } Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
        break;

    case 2:
        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n             MENU EXIBIR             \n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n{ 1 } Exibir Shinobi\n{ 2 } Exibir Clã\n{ 3 } Exibir Jutsu\n{ 4 } Exibir Missão\n{ 5 } Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
        break;

    case 3:
        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n             MENU ALTERAR            \n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n{ 1 } Alterar Shinobi\n{ 2 } Alterar Clã\n{ 3 } Alterar Jutsu\n{ 4 } Alterar Missão\n{ 5 } Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
        break;

    case 4:
        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n             MENU DELETAR            \n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n{ 1 } Deletar Shinobi\n{ 2 } Deletar Clã\n{ 3 } Deletar Jutsu\n{ 4 } Deletar Missão\n{ 5 } Voltar\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
        break;
    }
}

void SalvaDados()
{
    FILE *Dados;

    Dados = fopen("DadosTCA", "w");

    if (Dados == NULL)
    {
        printf("Erro! Arquivo Nao Encontrado.");
        exit(0);
    }
    else
    {
        if (_NumeroDoJutsu > 0)
        {
            fprintf(Dados, "%d,", _NumeroDoJutsu);
            for (int i = 0; i < _NumeroDoJutsu; i++)
            {
                fprintf(Dados, "%s,", _ConjuntoDeJutsus[i].Nome);
                fprintf(Dados, "%s,", _ConjuntoDeJutsus[i].TipoJutsu);
                fprintf(Dados, "%d,", _ConjuntoDeJutsus[i].CustodeChakra);
                fprintf(Dados, "%d,", _ConjuntoDeJutsus[i].PoderdeAtaque);
                fprintf(Dados, "%d,", _ConjuntoDeJutsus[i].QuantidadeDeElementos);

                if (_ConjuntoDeJutsus[i].QuantidadeDeElementos > 0)
                {
                    for (int j = 0; j < _ConjuntoDeJutsus[i].QuantidadeDeElementos; j++)
                    {
                        fprintf(Dados, "%s/", _ConjuntoDeJutsus[i].Elementos[j]);
                    }
                }
            }
            fprintf(Dados, ";");
        }

        if (_NumeroDoCla > 0)
        {
            fprintf(Dados, "%d,", _NumeroDoCla);
            for (int i = 0; i < _NumeroDoCla; i++)
            {
                fprintf(Dados, "%s,", _ConjuntoDeClas[i].NomeDoCla);
                fprintf(Dados, "%d,", _ConjuntoDeClas[i].QuantidadeDeJutsusHereditarios);
                fprintf(Dados, "%d,", _ConjuntoDeClas[i].QuantidadeDeJutsusTradicionais);
                if (_ConjuntoDeClas[i].QuantidadeDeJutsusHereditarios > 0)
                {
                    for (int j = 0; j < _ConjuntoDeClas[i].QuantidadeDeJutsusHereditarios; j++)
                    {
                        fprintf(Dados, "%s,", _ConjuntoDeClas[i].JutsusHereditarios[j]->Nome);
                        fprintf(Dados, "%s,", _ConjuntoDeClas[i].JutsusHereditarios[j]->TipoJutsu);
                        fprintf(Dados, "%d,", _ConjuntoDeClas[i].JutsusHereditarios[j]->CustodeChakra);
                        fprintf(Dados, "%d,", _ConjuntoDeClas[i].JutsusHereditarios[j]->PoderdeAtaque);
                        fprintf(Dados, "%d,", _ConjuntoDeClas[i].JutsusHereditarios[j]->QuantidadeDeElementos);

                        if (_ConjuntoDeClas[i].JutsusHereditarios[j]->QuantidadeDeElementos > 0)
                        {
                            for (int k = 0; k < _ConjuntoDeClas[i].JutsusHereditarios[j]->QuantidadeDeElementos; k++)
                            {
                                fprintf(Dados, "%s/", _ConjuntoDeClas[i].JutsusHereditarios[j]->Elementos[k]);
                            }
                        }
                    }
                }

                if (_ConjuntoDeClas[i].QuantidadeDeJutsusTradicionais > 0)
                {
                    for (int j = 0; j < _ConjuntoDeClas[i].QuantidadeDeJutsusTradicionais; j++)
                    {
                        fprintf(Dados, "%s,", _ConjuntoDeClas[i].JutsusTradicionais[j]->Nome);
                        fprintf(Dados, "%s,", _ConjuntoDeClas[i].JutsusTradicionais[j]->TipoJutsu);
                        fprintf(Dados, "%d,", _ConjuntoDeClas[i].JutsusTradicionais[j]->CustodeChakra);
                        fprintf(Dados, "%d,", _ConjuntoDeClas[i].JutsusTradicionais[j]->PoderdeAtaque);
                        fprintf(Dados, "%d,", _ConjuntoDeClas[i].JutsusTradicionais[j]->QuantidadeDeElementos);

                        if (_ConjuntoDeClas[i].JutsusTradicionais[j]->QuantidadeDeElementos > 0)
                        {
                            for (int k = 0; k < _ConjuntoDeClas[i].JutsusTradicionais[j]->QuantidadeDeElementos; k++)
                            {
                                fprintf(Dados, "%s/", _ConjuntoDeClas[i].JutsusTradicionais[j]->Elementos[k]);
                            }
                        }
                    }
                }
            }
            fprintf(Dados, ";");
        }

        if (_NumeroDoShinobi > 0)
        {
            fprintf(Dados, "%d,", _NumeroDoShinobi);
            for (int i = 0; i < _NumeroDoShinobi; i++)
            {
                fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].Nome);
                fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].Patente);
                fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].QuantidadeDeChakra);
                fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].QuantidadeDeJutsus);
                fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->NomeDoCla);
                fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios);
                fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais);
                fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].ElementoPrincipal);

                if (_ConjuntoDeShinobis[i].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios > 0)
                {
                    for (int j = 0; j < _ConjuntoDeShinobis[i].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios; j++)
                    {
                        fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->Nome);
                        fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->CustodeChakra);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->PoderdeAtaque);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos);

                        if (_ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos > 0)
                        {
                            for (int k = 0; k < _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos; k++)
                            {
                                fprintf(Dados, "%s/", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k]);
                            }
                        }
                    }
                }

                if (_ConjuntoDeShinobis[i].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais > 0)
                {
                    for (int j = 0; j < _ConjuntoDeShinobis[i].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais; j++)
                    {
                        fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->Nome);
                        fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->CustodeChakra);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->PoderdeAtaque);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos);

                        if (_ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos > 0)
                        {
                            for (int k = 0; k < _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos; k++)
                            {
                                fprintf(Dados, "%s/", _ConjuntoDeShinobis[i].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k]);
                            }
                        }
                    }
                }

                if (_ConjuntoDeShinobis[i].QuantidadeDeJutsus > 0)
                {
                    for (int j = 0; j < _ConjuntoDeShinobis[i].QuantidadeDeJutsus; j++)
                    {
                        fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].CaracteristicasDoJutsu[j]->Nome);
                        fprintf(Dados, "%s,", _ConjuntoDeShinobis[i].CaracteristicasDoJutsu[j]->TipoJutsu);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoJutsu[j]->CustodeChakra);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoJutsu[j]->PoderdeAtaque);
                        fprintf(Dados, "%d,", _ConjuntoDeShinobis[i].CaracteristicasDoJutsu[j]->QuantidadeDeElementos);

                        if (_ConjuntoDeJutsus[i].QuantidadeDeElementos > 0)
                        {
                            for (int k = 0; k < _ConjuntoDeShinobis[i].CaracteristicasDoJutsu[j]->QuantidadeDeElementos; k++)
                            {
                                fprintf(Dados, "%s/", _ConjuntoDeShinobis[i].CaracteristicasDoJutsu[j]->Elementos[k]);
                            }
                        }
                    }
                }
            }
            fprintf(Dados, ";");
        }
    }
    fclose(Dados);
}

void RecuperaDados()
{
    // int Separador = 0;
    int SeparadorDeContexto = 0;
    char LinhaDeDados[5096], *AuxTok, *DadosDoJutsu, *DadosDoCla, *DadoosDoShinobi;
    FILE *Dados;

    Dados = fopen("DadosTCA", "r");

    while ((fgets(LinhaDeDados, sizeof(LinhaDeDados), Dados)))
    {
        DadosDoJutsu = strtok(LinhaDeDados, ";");
        DadosDoCla = strtok(NULL, ";");
        DadoosDoShinobi = strtok(NULL, ";");
        while (1)
        {
            if (SeparadorDeContexto == 0)
            {
                _NumeroDoJutsu = 0;

                AuxTok = strtok(DadosDoJutsu, ",");
                int QuantidadeDeJutsu = atoi(AuxTok);
                _ConjuntoDeJutsus = (tJutsu *)malloc(QuantidadeDeJutsu * sizeof(tJutsu));

                for (int j = 0; j < QuantidadeDeJutsu; j++)
                {
                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeJutsus[_NumeroDoJutsu].Nome = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                    strcpy(_ConjuntoDeJutsus[_NumeroDoJutsu].Nome, AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeJutsus[_NumeroDoJutsu].TipoJutsu = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                    strcpy(_ConjuntoDeJutsus[_NumeroDoJutsu].TipoJutsu, AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeJutsus[_NumeroDoJutsu].CustodeChakra = atoi(AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeJutsus[_NumeroDoJutsu].PoderdeAtaque = atoi(AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeJutsus[_NumeroDoJutsu].QuantidadeDeElementos = atoi(AuxTok);
                    if (_ConjuntoDeJutsus[_NumeroDoJutsu].QuantidadeDeElementos > 0)
                    {
                        _ConjuntoDeJutsus[_NumeroDoJutsu].Elementos = (char **)malloc(_ConjuntoDeJutsus[_NumeroDoJutsu].QuantidadeDeElementos * sizeof(char *));

                        for (int i = 0; i < _ConjuntoDeJutsus[_NumeroDoJutsu].QuantidadeDeElementos; i++)
                        {
                            AuxTok = strtok(NULL, "/");
                            _ConjuntoDeJutsus[_NumeroDoJutsu].Elementos[i] = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeJutsus[_NumeroDoJutsu].Elementos[i], AuxTok);
                        }
                    }
                    else
                    {
                        _ConjuntoDeJutsus[_NumeroDoJutsu].Elementos = NULL;
                    }
                    _NumeroDoJutsu++;
                }
                SeparadorDeContexto++;
                continue;
            }
            else if (SeparadorDeContexto == 1)
            {
                _NumeroDoCla = 0;

                AuxTok = strtok(DadosDoCla, ",");
                int QuantidadeDeClas = atoi(AuxTok);
                _ConjuntoDeClas = (tCla *)malloc(QuantidadeDeClas * sizeof(tCla));

                for (int i = 0; i < QuantidadeDeClas; i++)
                {
                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeClas[_NumeroDoCla].NomeDoCla = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                    strcpy(_ConjuntoDeClas[_NumeroDoCla].NomeDoCla, AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusHereditarios = atoi(AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusTradicionais = atoi(AuxTok);

                    if (_ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusHereditarios > 0)
                    {
                        int j;
                        _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios = malloc(_ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusHereditarios * sizeof(tJutsu *));
                        for (j = 0; j < _ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusHereditarios; j++)
                        {
                            _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j] = malloc(sizeof(tJutsu));

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->Nome = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->Nome, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->TipoJutsu = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->TipoJutsu, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->CustodeChakra = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->PoderdeAtaque = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->QuantidadeDeElementos = atoi(AuxTok);

                            if (_ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->QuantidadeDeElementos > 0)
                            {
                                _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->Elementos = (char **)malloc(_ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->QuantidadeDeElementos * sizeof(char *));
                                for (int k = 0; k < _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->QuantidadeDeElementos; k++)
                                {
                                    AuxTok = strtok(NULL, "/");
                                    _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->Elementos[k] = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                                    strcpy(_ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->Elementos[k], AuxTok);
                                }
                            }
                            else
                            {
                                _ConjuntoDeClas[_NumeroDoCla].JutsusHereditarios[j]->Elementos = NULL;
                            }
                        }
                    }

                    if (_ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusTradicionais > 0)
                    {
                        int j;
                        _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais = malloc(_ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusTradicionais * sizeof(tJutsu *));
                        for (j = 0; j < _ConjuntoDeClas[_NumeroDoCla].QuantidadeDeJutsusTradicionais; j++)
                        {
                            _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j] = malloc(sizeof(tJutsu));

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->Nome = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->Nome, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->TipoJutsu = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->TipoJutsu, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->CustodeChakra = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->PoderdeAtaque = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->QuantidadeDeElementos = atoi(AuxTok);
                            if (_ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->QuantidadeDeElementos > 0)
                            {
                                _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->Elementos = (char **)malloc(_ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->QuantidadeDeElementos * sizeof(char *));

                                for (int k = 0; k < _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->QuantidadeDeElementos; k++)
                                {
                                    AuxTok = strtok(NULL, "/");
                                    _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->Elementos[k] = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                                    strcpy(_ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->Elementos[k], AuxTok);
                                }
                            }
                            else
                            {
                                _ConjuntoDeClas[_NumeroDoCla].JutsusTradicionais[j]->Elementos = NULL;
                            }
                        }
                    }
                    _NumeroDoCla++;
                }
                SeparadorDeContexto++;
                continue;
            }
            if (SeparadorDeContexto == 2)
            {
                _NumeroDoShinobi = 0;

                AuxTok = strtok(DadoosDoShinobi, ",");
                int QuantidadeDeShinobis = atoi(AuxTok);
                _ConjuntoDeShinobis = (tNinja *)malloc(QuantidadeDeShinobis * sizeof(tNinja));

                for (int i = 0; i < QuantidadeDeShinobis; i++)
                {
                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].Nome = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                    strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].Nome, AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].Patente = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                    strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].Patente, AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].QuantidadeDeChakra = atoi(AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].QuantidadeDeJutsus = atoi(AuxTok);

                    _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla = malloc(sizeof(tCla));

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->NomeDoCla = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                    strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->NomeDoCla, AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios = atoi(AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais = atoi(AuxTok);

                    AuxTok = strtok(NULL, ",");
                    _ConjuntoDeShinobis[_NumeroDoShinobi].ElementoPrincipal = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                    strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].ElementoPrincipal, AuxTok);

                    if (_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios > 0)
                    {
                        int j;
                        _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios = malloc( _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios * sizeof(tJutsu *));
                        for (j = 0; j < _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusHereditarios; j++)
                        {
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j] = malloc(sizeof(tJutsu));

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->Nome = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->Nome, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->TipoJutsu, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->CustodeChakra = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->PoderdeAtaque = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos = atoi(AuxTok);

                            if (_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos > 0)
                            {
                                _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos = (char **)malloc(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos * sizeof(char *));
                                for (int k = 0; k < _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->QuantidadeDeElementos; k++)
                                {
                                    AuxTok = strtok(NULL, "/");
                                    _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k] = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                                    strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos[k], AuxTok);
                                }
                            }
                            else
                            {
                                _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusHereditarios[j]->Elementos = NULL;
                            }
                        }
                    }

                    if (_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais > 0)
                    {
                        int j;
                        _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais = malloc( _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais * sizeof(tJutsu *));
                        for (j = 0; j < _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->QuantidadeDeJutsusTradicionais; j++)
                        {
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j] = malloc(sizeof(tJutsu));

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->Nome = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->Nome, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->TipoJutsu, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->CustodeChakra = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->PoderdeAtaque = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos = atoi(AuxTok);
                            if (_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos > 0)
                            {
                                _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos = (char **)malloc(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos * sizeof(char *));

                                for (int k = 0; k < _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->QuantidadeDeElementos; k++)
                                {
                                    AuxTok = strtok(NULL, "/");
                                    _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k] = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                                    strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos[k], AuxTok);
                                }
                            }
                            else
                            {
                                _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoCla->JutsusTradicionais[j]->Elementos = NULL;
                            }
                        }
                    }

                    if (_ConjuntoDeShinobis[_NumeroDoShinobi].QuantidadeDeJutsus > 0)
                    {
                        _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu = (tJutsu**)malloc(_ConjuntoDeShinobis[_NumeroDoShinobi].QuantidadeDeJutsus * sizeof(tJutsu*));
                        for (int j = 0; j < _ConjuntoDeShinobis[_NumeroDoShinobi].QuantidadeDeJutsus; j++)
                        {
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j] = malloc(sizeof(tJutsu));

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->Nome = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->Nome, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->TipoJutsu = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                            strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->TipoJutsu, AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->CustodeChakra = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->PoderdeAtaque = atoi(AuxTok);

                            AuxTok = strtok(NULL, ",");
                            _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->QuantidadeDeElementos = atoi(AuxTok);
                            if (_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->QuantidadeDeElementos > 0)
                            {
                                _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->Elementos = (char **)malloc(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->QuantidadeDeElementos * sizeof(char *));

                                for (int i = 0; i < _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->QuantidadeDeElementos; i++)
                                {
                                    AuxTok = strtok(NULL, "/");
                                    _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->Elementos[i] = (char *)malloc((strlen(AuxTok) + 1) * sizeof(char));
                                    strcpy(_ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->Elementos[i], AuxTok);
                                }
                            }
                            else
                            {
                                _ConjuntoDeShinobis[_NumeroDoShinobi].CaracteristicasDoJutsu[j]->Elementos = NULL;
                            }
                        }
                        congelar_tela();
                    }
                    _NumeroDoShinobi++;
                }
                break;
            }
        }
    }
    fclose(Dados);
}
