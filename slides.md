---
theme: default
background: https://gesternova.com/wp-content/uploads/2024/04/220209-Gesternova-001-1.jpg
title: Fluxor
layout: cover
highlighter: shiki
drawings:
  persist: false
transition: slide-left
mdc: true
---

# Fluxor

¿Qué es Fluxor y para que sirve?

---

# Contenido

<Toc minDepth="1" maxDepth="2"></Toc>

---
layout: center
---

<div class="text-center">

# ¿Qué es Fluxor?

Fluxor es una librería .NET que implementa el patrón Flux
</div>

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# ¿Qué es el patrón Flux?

Es un patrón de diseño para el manejo y el flujo de los datos de una aplicación

<div grid="~ cols-2 gap-2">
  <div v-click>
```mermaid
stateDiagram-v2
    Dispatcher --> Reducer1
    Dispatcher --> Reducer2
    Reducer1 --> State1
    Reducer2 --> State2
    State1 --> UserInterface
    State2 --> UserInterface
    UserInterface --> Button
    Button --> Dispatcher
```
  </div>
  <div>
    <h3 v-click>Reglas</h3>
    <ul>
      <li v-click>El estado <b>sólo</b> puede ser de lectura</li>
      <li v-click>Para alterar el estado de la aplicación se tiene que hacer a través de un <em>dispatcher</em></li>
      <li v-click>Cada <em>reducer</em> que gestiona una acción creará un nuevo estado reflejando el estado actual con los cambios que se piden</li>
      <li v-click>La vista usa el estado de la aplicación para pintar cosas</li>
    </ul>
  </div>
</div>

---

# Crear estados

```csharp {all|1|4|6|8-11|all} twoslash
[FeatureState]
public class CounterState
{
  public int ClickCount { get; }

  private CounterState() {} // Required for creating initial state

  public CounterState(int clickCount)
  {
    ClickCount = clickCount;
  }
}
```

<!--
Tenemos que definir una clase para reflejar el estado que queramos

[click] Tenemos que añadir el decorador

[click:1] Definimos las propiedades que queramos. IMPORTANTE solo de lectura

[click:1] Necesitamos un constructor sin parametros. Hacemos que sea privado

[click:1] Definimos un constructor publico que permite definir el valor de la accion

[click:1] Con todo eso, tenemos la clase al completo

-->

---

# Consultar el estado

```csharp {all|3|5|8|10-18|all} twoslash
public class App
{
  private readonly IState<CounterState> CounterState;

  public App(IState<CounterState> counterState)
  {
    CounterState = counterState;
    CounterState.StateChanged += CounterState_StateChanged;
  }

  private void CounterState_StateChanged(object sender, EventArgs e)
  {
    Console.WriteLine("");
    Console.WriteLine("==========================> CounterState");
    Console.WriteLine("ClickCount is " + CounterState.Value.ClickCount);
    Console.WriteLine("<========================== CounterState");
    Console.WriteLine("");
  }
}
```

---

# Cambiar el estado

Definimos una acción

```csharp
public class IncrementCounterAction {}
```

---

# Reducir la acción

