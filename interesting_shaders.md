# Fragment:

## Simple blur
```glsl
precision highp float;
varying highp vec2 vTextureCoord;

uniform sampler2D uSampler;
uniform float uA;

void main(void) {

  vec2 pos = vTextureCoord;
  float radius = uA * 4.0 / 512.0;

  vec3 sum = vec3(0);
  sum += texture2D(uSampler, pos + vec2(-radius)).rgb;
  sum += texture2D(uSampler, pos + vec2(0.0, -radius)).rgb;
  sum += texture2D(uSampler, pos + vec2(radius, -radius)).rgb;

  sum += texture2D(uSampler, pos + vec2(-radius, 0.0)).rgb;
  sum += texture2D(uSampler, pos + vec2(0.0)).rgb;
  sum += texture2D(uSampler, pos + vec2(radius, 0.0)).rgb;

  sum += texture2D(uSampler, pos + vec2(-radius, radius)).rgb;
  sum += texture2D(uSampler, pos + vec2(0.0, radius)).rgb;
  sum += texture2D(uSampler, pos + vec2(radius, radius)).rgb;

  gl_FragColor = vec4((1.0 / 9.0) * sum, 1.0);
}
```

## Gaussian blur
```glsl
precision highp float;
varying highp vec2 vTextureCoord;

uniform sampler2D uSampler;
uniform float uA;

void main(void) {

  vec2 pos = vTextureCoord;
  float radius = uA * 4.0 / 512.0;

  vec3 sum = vec3(0);
  sum += texture2D(uSampler, pos + vec2(-radius)).rgb;
  sum += texture2D(uSampler, pos + vec2(0.0, -radius)).rgb * 2.0;
  sum += texture2D(uSampler, pos + vec2(radius, -radius)).rgb;

  sum += texture2D(uSampler, pos + vec2(-radius, 0.0)).rgb * 2.0;
  sum += texture2D(uSampler, pos + vec2(0.0)).rgb * 4.0;
  sum += texture2D(uSampler, pos + vec2(radius, 0.0)).rgb * 2.0;

  sum += texture2D(uSampler, pos + vec2(-radius, radius)).rgb;
  sum += texture2D(uSampler, pos + vec2(0.0, radius)).rgb * 2.0;
  sum += texture2D(uSampler, pos + vec2(radius, radius)).rgb;

  gl_FragColor = vec4((1.0 / 9.0) * sum, 1.0);
}
```
