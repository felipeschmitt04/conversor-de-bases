# Conversor de Bases e Representações Binárias

Uma aplicação web simples para conversão de números entre diferentes bases (binário, octal, decimal, hexadecimal, etc.), representação de números negativos em binário (sinal-magnitude, complemento de 1 e complemento de 2) e operações aritméticas entre números de diferentes bases.

---

## ✨ Funcionalidades

- Converter números entre bases de 2 a 36.
- Representar números negativos em binário utilizando:
  - Sinal e Magnitude
  - Complemento de 1
  - Complemento de 2
- Realizar operações aritméticas (+, -, ×, ÷) entre números de diferentes bases.

---

## 🚀 Como Usar

1. Clone o repositório:
   ```
   git clone https://github.com/felipeschmitt04/conversor-de-bases.git
   ```

2. Acesse o diretório do projeto:
   ```
   cd conversor-de-bases
   ```

3. Abra o arquivo `index.html`:
   - Basta abrir o `index.html` no seu navegador preferido (Chrome, Firefox, Edge, etc.).
   - Ou use extensões como "Live Server" no VS Code para recarregar automaticamente.

---

## 🛠️ Tecnologias Utilizadas

- HTML5
- JavaScript (Vanilla)
- TailwindCSS (via CDN)

---

## 📚 Estrutura do Projeto

```
├── index.html      # Arquivo principal com a estrutura, lógica e estilo do app
```

---

## 🧩 Funções

### 🔹 decimalParaBase(numero, base, casasDecimais = 5)

**Descrição:**  
Converte um número decimal para uma representação em outra base (de 2 a 36), incluindo a parte fracionária.

**Parâmetros:**
- `numero` (*number*): número em base decimal a ser convertido.
- `base` (*number*): base de destino.
- `casasDecimais` (*number*, opcional): número de casas decimais a serem consideradas na conversão.

**Retorno:**
- (*string*): número convertido para a base desejada.

**Código:**
```javascript
function decimalParaBase(numero, base, casasDecimais = 5) {
    const digitos = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    let resultado = "";

    let negativo = numero < 0;
    numero = Math.abs(numero);

    let parteInteira = Math.floor(numero);
    let parteFracionaria = numero - parteInteira;

    do {
        resultado = digitos[parteInteira % base] + resultado;
        parteInteira = Math.floor(parteInteira / base);
    } while (parteInteira > 0);

    if (parteFracionaria > 0) {
        resultado += ".";
        for (let i = 0; i < casasDecimais && parteFracionaria !== 0; i++) {
            parteFracionaria *= base;
            let digito = Math.floor(parteFracionaria);
            resultado += digitos[digito];
            parteFracionaria -= digito;
        }
    }

    return negativo ? "-" + resultado : resultado;
}
```

---

### 🔹 baseParaDecimal(numero, base)

**Descrição:**  
Converte um número representado em uma base qualquer (2 a 36) para seu valor decimal.

**Parâmetros:**
- `numero` (*string*): número a ser convertido, pode conter parte fracionária.
- `base` (*number*): base original do número.

**Retorno:**
- (*number*): valor decimal equivalente ao número fornecido.

**Código:**
```javascript
function baseParaDecimal(numero, base) {
    const digitos = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    numero = numero.toUpperCase().replace(",", ".");
    let negativo = false;

    if (numero[0] === '-') {
        negativo = true;
        numero = numero.slice(1);
    }

    let [parteInteira, parteFracionaria = ""] = numero.split(".");
    let resultado = 0;

    for (let c of parteInteira) {
        let valor = digitos.indexOf(c);
        if (valor >= base || valor === -1) return NaN;
        resultado = resultado * base + valor;
    }

    let fator = 1 / base;
    for (let c of parteFracionaria) {
        let valor = digitos.indexOf(c);
        if (valor >= base || valor === -1) return NaN;
        resultado += valor * fator;
        fator /= base;
    }

    return negativo ? -resultado : resultado;
}
```

---

### 🔹 converterBases()

**Descrição:**  
Realiza a conversão entre bases diferentes a partir dos dados inseridos no formulário da aba "Conversão".

**Parâmetros:**
- Nenhum (usa os valores dos inputs HTML).

**Retorno:**
- Atualiza o conteúdo do elemento `resultadoConversao` com o número convertido ou mensagem de erro.

**Código:**
```javascript
function converterBases() {
    let numero = document.getElementById("numero").value;
    let baseOrigem = parseInt(document.getElementById("baseOrigem").value);
    let baseDestino = parseInt(document.getElementById("baseDestino").value);

    if (baseOrigem < 2 || baseOrigem > 36 || baseDestino < 2 || baseDestino > 36) {
        document.getElementById("resultadoConversao").innerText = "Base inválida.";
        return;
    }

    let decimal = baseParaDecimal(numero, baseOrigem);
    if (isNaN(decimal)) {
        document.getElementById("resultadoConversao").innerText = "Erro: número ou base inválida.";
    } else {
        let casasDecimais = (numero.includes('.') || numero.includes(',')) ? 10 : 0;
        let convertido = decimalParaBase(decimal, baseDestino, casasDecimais);
        document.getElementById("resultadoConversao").innerText = `Resultado: ${convertido}`;
    }
}
```

---

### 🔹 sinalMagnitude(numero, bits)

**Descrição:**  
Gera a representação em sinal e magnitude de um número inteiro com o número de bits especificado.

**Parâmetros:**
- `numero` (*number*): número inteiro a ser representado.
- `bits` (*number*): quantidade de bits totais.

**Retorno:**
- (*string*): representação binária em sinal e magnitude.

**Código:**
```javascript
function sinalMagnitude(numero, bits) {
    let sinal = numero < 0 ? "1" : "0";
    let magnitude = Math.abs(numero).toString(2).padStart(bits - 1, '0');
    return sinal + magnitude;
}
```

---

### 🔹 complemento1(numero, bits)

**Descrição:**  
Calcula o complemento de 1 de um número inteiro com o número de bits especificado.

**Parâmetros:**
- `numero` (*number*): número inteiro a ser representado.
- `bits` (*number*): quantidade de bits totais.

**Retorno:**
- (*string*): representação binária em complemento de 1.

**Código:**
```javascript
function complemento1(numero, bits) {
    let binario = Math.abs(numero).toString(2).padStart(bits, '0');
    if (numero >= 0) return binario;

    return binario.split('').map(bit => bit === '0' ? '1' : '0').join('');
}
```

---

### 🔹 complemento2(numero, bits)

**Descrição:**  
Calcula o complemento de 2 de um número inteiro com o número de bits especificado.

**Parâmetros:**
- `numero` (*number*): número inteiro a ser representado.
- `bits` (*number*): quantidade de bits totais.

**Retorno:**
- (*string*): representação binária em complemento de 2.

**Código:**
```javascript
function complemento2(numero, bits) {
    if (numero >= 0) return Math.abs(numero).toString(2).padStart(bits, '0');

    let comp1 = complemento1(numero, bits).split('');
    for (let i = bits - 1; i >= 0; i--) {
        if (comp1[i] === '0') {
            comp1[i] = '1';
            break;
        } else {
            comp1[i] = '0';
        }
    }
    return comp1.join('');
}
```

---

### 🔹 representarBinario()

**Descrição:**  
Recebe um número inteiro e uma quantidade de bits do formulário e gera as representações:
- Sinal e Magnitude
- Complemento de 1
- Complemento de 2

**Parâmetros:**
- Nenhum (usa os valores dos inputs HTML).

**Retorno:**
- Atualiza o conteúdo do elemento `resultadoBinario` com as representações binárias ou mensagens de erro.

**Código:**
```javascript
function representarBinario() {
    let numero = parseInt(document.getElementById("numeroNegativo").value);
    let bits = parseInt(document.getElementById("bits").value);

    if (isNaN(numero) || isNaN(bits) || bits < 2) {
        document.getElementById("resultadoBinario").innerText = "Dados inválidos.";
        return;
    }

    let limiteSinalMagnitude = (1 << (bits - 1)) - 1; 
    let minComplemento2 = -(1 << (bits - 1));      
    let maxComplemento2 = (1 << (bits - 1)) - 1;    

    let output = "";

    if (numero >= -limiteSinalMagnitude && numero <= limiteSinalMagnitude) {
        output += "Sinal e Magnitude: " + sinalMagnitude(numero, bits) + "\n";
        output += "Complemento de 1: " + complemento1(numero, bits) + "\n";
    } else {
        output += "Sinal e Magnitude: Valor excede intervalo.\n";
        output += "Complemento de 1: Valor excede intervalo.\n";
    }

    if (numero >= minComplemento2 && numero <= maxComplemento2) {
        output += "Complemento de 2: " + complemento2(numero, bits);
    } else {
        output += "Complemento de 2: Valor excede intervalo.";
    }

    document.getElementById("resultadoBinario").innerText = output;
}
```

---

### 🔹 realizarOperacao()

**Descrição:**  
Realiza uma operação matemática (+, -, ×, ÷) entre dois números de bases diferentes, convertendo o resultado para uma base final.

**Parâmetros:**
- Nenhum (usa os valores dos inputs HTML).

**Retorno:**
- Atualiza o conteúdo do elemento `resultadoOperacao` com o resultado da operação ou mensagem de erro.

**Código:**
```javascript
function realizarOperacao() {
    let numero1 = document.getElementById("numero1").value;
    let base1 = parseInt(document.getElementById("base1").value);
    let operacao = document.getElementById("operacao").value;
    let numero2 = document.getElementById("numero2").value;
    let base2 = parseInt(document.getElementById("base2").value);
    let baseResultado = parseInt(document.getElementById("baseResultado").value);

    if (base1 < 2 || base1 > 36 || base2 < 2 || base2 > 36 || baseResultado < 2 || baseResultado > 36) {
        document.getElementById("resultadoOperacao").innerText = "Base inválida.";
        return;
    }

    let decimal1 = baseParaDecimal(numero1, base1);
    let decimal2 = baseParaDecimal(numero2, base2);

    if (isNaN(decimal1) || isNaN(decimal2)) {
        document.getElementById("resultadoOperacao").innerText = "Erro: número ou base inválida.";
        return;
    }

    let resultado;
    switch (operacao) {
        case "+":
            resultado = decimal1 + decimal2;
            break;
        case "-":
            resultado = decimal1 - decimal2;
            break;
        case "*":
            resultado = decimal1 * decimal2;
            break;
        case "/":
            if (decimal2 === 0) {
                document.getElementById("resultadoOperacao").innerText = "Erro: divisão por zero.";
                return;
            }
            resultado = decimal1 / decimal2;
            break;
    }

    let resultadoConvertido = decimalParaBase(resultado, baseResultado, 10);
    document.getElementById("resultadoOperacao").innerText = `Resultado: ${resultadoConvertido}`;
}
```

---

### 🔹 mostrarAba(abaId)

**Descrição:**  
Alterna a visualização entre as abas "Conversão", "Binário" e "Operações" da interface.

**Parâmetros:**
- `abaId` (*string*): id da aba a ser exibida (`conversao`, `binario`, ou `operacoes`).

**Retorno:**
- Atualiza a classe CSS dos elementos para mostrar a aba selecionada e esconder as demais.

**Código:**
```javascript
function mostrarAba(abaId) {
    document.getElementById("abaConversao").style.display = abaId === "conversao" ? "block" : "none";
    document.getElementById("abaBinario").style.display = abaId === "binario" ? "block" : "none";
    document.getElementById("abaOperacoes").style.display = abaId === "operacoes" ? "block" : "none";
}
```

---
