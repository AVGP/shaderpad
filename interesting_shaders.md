# Note on the four sliders:

You can make your shaders interactive by using the uniforms they export to: `uA`, `uB`, `uC` and `uD` like this:

```glsl
  precision highp float;
  varying highp vec2 vTextureCoord;

  uniform sampler2D uSampler;
  
  // access the slider values - each exposes a single float value between 0.0 and 1.0:
  uniform float uA;
  uniform float uB;
  uniform float uC;
  uniform float uD;  

void main(void) {
  // uA = red, uB = green, uC = blue, uD = alpha
  gl_FragColor = vec4(uA, uB, uC, uD);
}
```

# Fragment shaders:

## Simple blur
```glsl
precision highp float;
varying highp vec2 vTextureCoord;

uniform sampler2D uSampler;
uniform float uA;

void main(void) {

  vec2 pos = vTextureCoord;
  float radius = uA * 4.0 / 1024.0;

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
avgp.github.io/shaderpad/a
uniform sampler2D uSampler;
uniform float uA;

void main(void) {

  vec2 pos = vTextureCoord;
  float radius = uA * 4.0 / 1024.0;

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

  gl_FragColor = vec4((1.0 / 16.0) * sum, 1.0);
}
```

## Edges

```glsl
precision highp float;

varying highp vec2 vTextureCoord;

uniform sampler2D uSampler;
uniform float uA;

float resolution = 1024.0;

void main(void) {
  float x = 1.0 / resolution;
  float y = 1.0 / resolution;

  vec4 horizEdge = vec4( 0.0 );
  horizEdge -= texture2D( uSampler, vec2( vTextureCoord.x - x, vTextureCoord.y - y ) ) * 1.0;
  horizEdge -= texture2D( uSampler, vec2( vTextureCoord.x - x, vTextureCoord.y     ) ) * 2.0;
  horizEdge -= texture2D( uSampler, vec2( vTextureCoord.x - x, vTextureCoord.y + y ) ) * 1.0;
  horizEdge += texture2D( uSampler, vec2( vTextureCoord.x + x, vTextureCoord.y - y ) ) * 1.0;
  horizEdge += texture2D( uSampler, vec2( vTextureCoord.x + x, vTextureCoord.y     ) ) * 2.0;
  horizEdge += texture2D( uSampler, vec2( vTextureCoord.x + x, vTextureCoord.y + y ) ) * 1.0;

  vec4 vertEdge = vec4( 0.0 );
  vertEdge -= texture2D( uSampler, vec2( vTextureCoord.x - x, vTextureCoord.y - y ) ) * 1.0;
  vertEdge -= texture2D( uSampler, vec2( vTextureCoord.x    , vTextureCoord.y - y ) ) * 2.0;
  vertEdge -= texture2D( uSampler, vec2( vTextureCoord.x + x, vTextureCoord.y - y ) ) * 1.0;
  vertEdge += texture2D( uSampler, vec2( vTextureCoord.x - x, vTextureCoord.y + y ) ) * 1.0;
  vertEdge += texture2D( uSampler, vec2( vTextureCoord.x    , vTextureCoord.y + y ) ) * 2.0;
  vertEdge += texture2D( uSampler, vec2( vTextureCoord.x + x, vTextureCoord.y + y ) ) * 1.0;
  vec3 edge = sqrt((horizEdge.rgb * horizEdge.rgb) + (vertEdge.rgb * vertEdge.rgb));
  if(((edge.r + edge.g + edge.b) / 3.0) >= uA) {
    gl_FragColor = vec4( 1.0 );
  } else {
    gl_FragColor = vec4( 0.0, 0.0, 0.0, 1.0 );
  }
}
```

# Vertex shaders:

## Rotate

```
attribute vec2 aVertexPosition;
attribute vec2 aTextureCoord;

varying highp vec2 vTextureCoord;

uniform float uA;

mat2 rotate(float radians) {
  radians *= 2.0 * 3.1415;
  return mat2( cos(radians), sin(radians),
              -sin(radians), cos(radians));
}

void main(void) {
  vTextureCoord = aTextureCoord;
  gl_Position = vec4(aVertexPosition * rotate(uA), 0.0, 1.0);
}
```
