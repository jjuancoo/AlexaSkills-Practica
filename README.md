# Creando una skill para Alexa
-----

## DESCRIPCION:

Proyecto de Clase para la Unidad 2 de la Asignatura de Desarrollo Móvil Integral (DMI) impartida
por el M.T.I Marco A. Ramírez Hernández

## HISTORIAL DE PRÁCTICAS:
|No. |Nombre|Potenciador|Estatus|
|--|--|--|--|
|26|Creación de una skill para alexa|10|Activa|
|07|Tarea sobre el reporte|10|Activa|

## LISTA DE HERRAMIENTAS
![DART](https://img.shields.io/badge/Dart-0175C2?style=for-the-badge&logo=dart&logoColor=white)

## AUTOR
Elaborado por: Juan Manuel Cruz Ortiz [@jjuancoo](https://github.com/jjuancoo)

|No. |Nombre|Materia|Carrera|
|--|--|--|--|
|00|Juan Manuel Cruz Ortiz|Desarrollo Movil Integrador|Ingenieria en Desarrollo y Gestion de Software|

## PRACTICA 26: Definir la funcionalidad e UI para la aplicación de Yes Not
### Objetivo 

Crear una skill personalizada para obtener el clima de una region en particular (Xicotepec de Juarez), asi como configurarla y vincularlar a una API para obtener el clima llamda opeweather.

### Descripcion
Crear una sckill para alexa, aprendiendo a utilizar la consola para desarrolladores de amazon developers, creando la invocacion personlizada, asi como vinculado la apikey dentro del codigo.

## Evidencia y Codigo
### Codigo index.js
    const Alexa = require('ask-sdk-core');
    const axios = require('axios');
    
    const LaunchRequestHandler = {
      canHandle(handlerInput) {
        return Alexa.getRequestType(handlerInput.requestEnvelope) === 'LaunchRequest';
      },
      handle(handlerInput) {
        const speakOutput = 'Esta skill esta creada por Juan Manuel Cruz, que deseas hacer?';
    
        return handlerInput.responseBuilder
          .speak(speakOutput)
          .reprompt(speakOutput)
          .getResponse();
      }
    };
    
    const WeatherIntentHandler = {
      canHandle(handlerInput) {
        return (
          Alexa.getRequestType(handlerInput.requestEnvelope) === 'IntentRequest' &&
          Alexa.getIntentName(handlerInput.requestEnvelope) === 'Clima'
        );
      },
      async handle(handlerInput) {
        try {
          const apiKey = 'adb8240a324c1aac01e0e1f92e4c3400'; // Reemplaza con tu clave de API de OpenWeatherMap
          const lat = 19.050; // Reemplaza con la latitud deseada
          const lon = -98.200; // Reemplaza con la longitud deseada
    
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${apiKey}&units=metric`
          );
    
          const temperature = response.data.main.temp;
          const weatherDescription = response.data.weather[0].description;
          const location = response.data.name;
          const humedad = response.data.main.humidity;
          const tem_max = response.data.main.temp_min;
          //NUEVO
          const sepone = response.data.sys.sunset;
          const sunsetDate = new Date(sepone);
          const sunsetHour = sunsetDate.getHours();
          const sunsetMinutes = sunsetDate.getMinutes();
    
          
    
          const speakOutput = `La temperatura actual en ${location} es de ${temperature} grados Celsius y la humedad es de ${humedad} y el sol se oculta a las ${sunsetHour}:${sunsetMinutes}.`;
    
          return handlerInput.responseBuilder.speak(speakOutput).getResponse();
        } catch (error) {
          console.log('Error al obtener el clima:', error);
    
          const speakOutput = 'Lo siento, no pude obtener la información del clima en este momento.';
          return handlerInput.responseBuilder.speak(speakOutput).getResponse();
        }
      },
    };
    
    const HelpIntentHandler = {
      canHandle(handlerInput) {
        return (
          Alexa.getRequestType(handlerInput.requestEnvelope) === 'IntentRequest' &&
          Alexa.getIntentName(handlerInput.requestEnvelope) === 'AMAZON.HelpIntent'
        );
      },
      handle(handlerInput) {
        const speakOutput = 'Puedes preguntarme sobre el clima diciendo: ¿Cuál es el clima actual?';
    
        return handlerInput.responseBuilder.speak(speakOutput).getResponse();
      },
    };
    
    const CancelAndStopIntentHandler = {
      canHandle(handlerInput) {
        return (
          Alexa.getRequestType(handlerInput.requestEnvelope) === 'IntentRequest' &&
          (Alexa.getIntentName(handlerInput.requestEnvelope) === 'AMAZON.CancelIntent' ||
            Alexa.getIntentName(handlerInput.requestEnvelope) === 'AMAZON.StopIntent')
        );
      },
      handle(handlerInput) {
        const speakOutput = '¡Hasta luego!';
    
        return handlerInput.responseBuilder.speak(speakOutput).getResponse();
      },
    };
    
    const FallbackIntentHandler = {
      canHandle(handlerInput) {
        return (
          Alexa.getRequestType(handlerInput.requestEnvelope) === 'IntentRequest' &&
          Alexa.getIntentName(handlerInput.requestEnvelope) === 'AMAZON.FallbackIntent'
        );
      },
      handle(handlerInput) {
        const speakOutput = 'Lo siento, no puedo ayudarte con eso. Por favor, inténtalo de nuevo.';
    
        return handlerInput.responseBuilder.speak(speakOutput).getResponse();
      },
    };
    
    const SessionEndedRequestHandler = {
      canHandle(handlerInput) {
        return Alexa.getRequestType(handlerInput.requestEnvelope) === 'SessionEndedRequest';
      },
      handle(handlerInput) {
        console.log(`Sesión terminada: ${JSON.stringify(handlerInput.requestEnvelope)}`);
        return handlerInput.responseBuilder.getResponse();
      },
    };
    
    const IntentReflectorHandler = {
      canHandle(handlerInput) {
        return Alexa.getRequestType(handlerInput.requestEnvelope) === 'IntentRequest';
      },
      handle(handlerInput) {
        const intentName = Alexa.getIntentName(handlerInput.requestEnvelope);
        const speakOutput = `Acabas de activar la intención ${intentName}.`;
    
        return handlerInput.responseBuilder.speak(speakOutput).getResponse();
      },
    };
    
    const ErrorHandler = {
      canHandle() {
        return true;
      },
      handle(handlerInput, error) {
        console.log(`Error manejado: ${JSON.stringify(error)}`);
    
        const speakOutput = 'Lo siento, hubo un problema al procesar tu solicitud. Por favor, inténtalo de nuevo.';
    
        return handlerInput.responseBuilder.speak(speakOutput).getResponse();
      },
    };
    
    exports.handler = Alexa.SkillBuilders.custom()
      .addRequestHandlers(
        LaunchRequestHandler,
        WeatherIntentHandler,
        HelpIntentHandler,
        CancelAndStopIntentHandler,
        FallbackIntentHandler,
        SessionEndedRequestHandler,
        IntentReflectorHandler
      )
      .addErrorHandlers(ErrorHandler)
      .lambda();

### Utilización de la API key
          const apiKey = 'adb8240a324c1aac01e0e1f92e4c3400'; // Reemplaza con tu clave de API de OpenWeatherMap

### Capturas de pantalla

![Captura de pantalla 2024-12-05 084943](https://github.com/user-attachments/assets/c072e887-25ab-441f-b07b-920d08ec80f8)

