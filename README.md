# Algoritmos Genéticos para a Escolha de uma Mistura Binária de Combustíveis

Olá, esse é o repositório do trabalho final da Disciplina de Algoritmos Genéticos e Redes Neurais, ministrada pelo professor Daniel Cassar na Ilum Escola de Ciência durante o terceiro semestre do curso de Bacharelado em Ciência e Tecnologia, ao longo do primeiro semestre de 2023. Nesse sentido, o trabalho por Isadora Marcondes, Joâo Caramês e Raphaella Siqueira.

## Autores

- [Isadora Marcondes](https://github.com/isadoramarcondes)
- Email: isadora220051@ilum.cnpem.br

- [João Caramês](https://github.com/JoaoCarames)
- Email: joao220064@ilum.cnpem.br

- [Raphaella Siqueira](https://github.com/raphaella220046)
- Email: raphaella220046@ilum.cnpem.br

## Contextualização

No âmbito da disciplina de Termodinâmica Avançada, ministrada pelo professor Amauri Jardim de Paula, foi realizado ao longo do semestre, concomitantemente à essa disciplina, um projeto. Esse projeto está ilustrado na imagem abaixo:

<p align="center">
  <img src="https://github.com/isadoramarcondes/Trabalho_Redes/tree/main/Figuras%20Readme" alt="Projeto de Termodinâmica" width="80" height="80">
</p>

Nele, tivemos a missão de escolher uma mistura binária, formada então por dois compostos, para passar pelas etapas ilustradas, das quais destacamos dois processos, a separação de mistura (destilação fracionada na maioria dos casos) e o ciclo de rankine. Nesse sentido, usaremos esses dois processos para orientar a buscar pela melhor mistura binária.

Destilação: vários fatores podem influenciar a facilidade do processo, em especial a diferença de temperatura de ebulição dos compostos misturados. Assim, quão maior for essa diferença, mais fácil é separar os compostos.

Ciclo de Rankine: o ciclo de rankine é dividido nas seguintes quatro etapas;

1. Boiler (Aquecimento Isobárico):
Primeiro, acontece o processo de aquecimento isobaricamente, ou seja, a pressão se mantém constante durante esse processo. Nesse estágio, a água, é aquecida a alta pressão em uma caldeira, na qual o aquecimento ocorre até que a água atinja a temperatura de ebulição e se transforme em vapor. É aqui que entra o nosso combustível, o qual será usado para aquecer a água.

2. Turbina (Expansão Isentrópica):
Após o aquecimento na caldeira, o vapor de alta pressão e temperatura vai à turbina, onde passa por uma expansão isentrópica, ou seja, uma expansão adiabática reversível, na qual a entropia do sistema não sofre variação. Ao longo da expansão, o vapor libera energia cinética e gera trabalho. Assim, temos que a energia térmica do vapor é convertida em energia mecânica no eixo da turbina, a qual está conectado a um gerador para produção de eletricidade.

3. Condensador (Resfriamento Isobárico):
Depois de sair da turbina, o vapor agora de baixa pressão é vai ao condensador, no qual é resfriado isobaricamente e condensado de volta para a forma líquida. Nesse sentido, o calor é transferido do vapor para uma tubulação contendo água fria (de um rio ou do mar, geralmente).

4. Bomba (Compressão Isentrópica):
Por fim, a água vai para a bomba, a qual a comprime isentropicamente, aumentando a pressão sobre a água e mandando ela para a caldeira.

Assim, na etapa em que a substância de trabalho (água) está no boiler, temos que usar de um combustível para aquecê-la. Dessa forma, um composto com alta entalpia de combustão é desejado.

## Objetivos

### Objetivo Principal

> Usar o algoritmo genético para realizar a escolha da melhor mistura binária possível.

### Objetivos Secundários

> Trabalhar diferentes dados termodinâmicos e otimizá-los para atender o problema.
> Mostrar que é possível utilizar o algoritmo genético sobre dados termodinâmicos para realizar uma escolha.
> Trabalhar uma implementação de avaliação por meio de critérios envolvidos na escolha baseados no problema apresentado.

## Materiais e Métodos

Para realizar nosso objetivo, usamos do algoritmo genético e de uma base de dados termodinâmicos. Sendo assim, há nesse repositório um notebook para cada um.

### Organizando Dados Termodinâmicos

Nesse notebook, trabalhamos a construção de uma tabela organizada de possíveis dados termodinâmicos a serem utilizados ao longo do desenvolvimento do algoritmo genéticos e isso foi feito dada a dificuldade em encontrar de maneira simples e acessível esses dados dessa forma online. 

Nesse contexto, utilizamos de início os dados presentes na tabela qm9, os quais foram obtidos da Quantum Machine 9 (QM9), a qual disponibiliza dados obtidos por cálculos de DFT para mais de 100000 (cem mil) moléculas pequenas, as quais possuem até 9 átomos diferentes do hidrogênio na sua composição (Carbono, Oxigênio, Nitrogênio e Fósforo, no caso). Nessa tabela, encontramos dados preciosos para esse trabalho, como o código SMILES (identificador de moléculas), a entalpia de atomização, a entropia e a energia livre de Gibbs padrões. Todavia, esses dados estão quantificados na unidade Hartree (própria para calculos de DFT) e foram convertidos para as unidades usuais (baseadas na entalpia de formação) usando os dados de referência disponibilizados por eles.

Ademais, usamos os dados convertidos para estimar a entalpia de combustão de cada molécula presente no dataset, mas antes de entrarmos nessa parte, vale ressaltar os possíveis erros de aproximação obtidos pela conversão citada. Desse modo, a conversão pode gerar discrepâncias dada a natureza das medições, já que a entalpia de atomização faz referência a energia necessária para separar uma molécula em núcleos e elétrons e a entalpia de formação para separar em elementos. Segue um exemplo da discrepância dos valores para o etanol:

$$C_2H_6O \xrightarrow{} \Delta H_{atomization} = -97243.5744747 \text{kcal/mol} \\ C_2H_6O \xrightarrow{} \Delta H_{formation} = -66.39579 \text{kcal/mol}$$

Assim, mesmo utilizando as referências para a correção, esse tipo de diferença pode gerar imprecisão, como no caso do etanol, cujo valor obtido foi:

$$C_2H_6O \xrightarrow{} \Delta H_{formation} = 771.180433 \text{kcal/mol}$$

Vale notar que o valor é positivo por mera convenção e isso não afeta os cálculos detalhados no notebook. Então, o motivo de escolha desses dados foi a alta quantidade de moléculas, o que nos possibilita uma alta diversidade de dados e entendemos que isso é o mais importante para nossos objetivos, sendo então indicada uma avaliação do usuário ao final do processo.

Após isso, é realizada a reação de combustão e a estimativa da entalpia de combustão de cada molécula por meio de ferramentas de string e dicionários. Com isso, temos ao final os seguintes dados em nossa tabela:

<table ><tr><th>Fórmula<th><th>N° de Carbonos<th><th>N° de Hidrogênios<th><th>N° de Oxigênios<th><th>N° de Nitrogênios<th><th>N° de Fósforos<th><th>Isomeric Smiles<th><th>Smiles<th><th>Massa Molar (g/mol)<th><th>Entalpia de Combustão (kcal/mol)<th><th>Entalpia de Formação (kcal/mol)<th><th>Energia Interna (kcal)<th><th>Energia Livre de Gibbs (kcal/mol)<tr><tr>
<tr><td> C2H6O <td><td> 2 <td><td> 6 <td><td> 1 <td><td> 0 <td><td> 0 <td><td> CCO <td><td> CCO <td><td> 46.069 <td><td> 294.320086 <td><td> 771.180433 <td><td> 766.438343 <td><td> 709.349402 <td><tr> <table>

### Algoritmo Genético

aa


---

```python
# Exemplo de código Python
def hello_world():
    print("Hello, world!")

hello_world()