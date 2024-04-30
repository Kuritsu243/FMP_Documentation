# LightFlicker.cs
<show-structure depth="2" />

## Description
This script is used to simulate a light component flickering, by changing the intensity value.
## Script
```C#
using System.Collections.Generic;
using UnityEngine;
using Random = UnityEngine.Random;

namespace Environment.Lighting
{
    public class LightFlicker : MonoBehaviour
    {
        [SerializeField] private Color lightFlickerColour;
        [SerializeField] private float minIntensity;
        [SerializeField] private float maxIntensity;
        [SerializeField] private bool enableFlicker;
        [SerializeField] private int lightSmoothing = 8;


        private float _lastSum;
        private Light _light;
        private Queue<float> _lightQueue;
        
        private void Start()
        {
            _light = GetComponentInChildren<Light>();

            if (enableFlicker && lightSmoothing > 0)
                _lightQueue = new Queue<float>(lightSmoothing);
        }

        private void FixedUpdate()
        {
            if (!enableFlicker && lightSmoothing > 0) return;

            while (_lightQueue.Count >= lightSmoothing)
                _lastSum -= _lightQueue.Dequeue();

            var newVal = Random.Range(minIntensity, maxIntensity);
            _lightQueue.Enqueue(newVal);
            _lastSum += newVal;
            _light.intensity = _lastSum / (float)_lightQueue.Count;
        }

        private void Reset()
        {
            _lightQueue.Clear();
            _lastSum = 0;
        }
    }
}
```
{collapsed-title="LightFlicker.cs" collapsible="true"}

## Private Methods
{type="wide" sorted="asc"}
Start()
: Gets the light component, and if flicker is enabled starts the queue for the new intensity values.

FixedUpdate()
: Generates a new light intensity, between the max and min light intensity values,
and queues it to be set as the current active intensity. 

Reset()
: Clears the queue of the light intensity values.

## Variables
{type="wide" sorted="asc"}
lightFlickerColour
: The colour of the light.

minIntensity
: The minimum intensity for the light.

maxIntensity
: The maximum intensity for the light.

enableFlicker
: Should the light flicker.

lightSmoothing
: How smoothly will the light go to the next intensity.

_lastSum
: The last value, incremented by the intensity divided by the number of values in the queue

_light
: The light component being controlled.

_lightQueue
: The queue controlling the light intensity.

