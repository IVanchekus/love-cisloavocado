<template>
  <canvas ref="heartCanvas"></canvas>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

const heartCanvas = ref<HTMLCanvasElement | null>(null)

onMounted(() => {
  if (!heartCanvas.value) return

  const canvas = heartCanvas.value
  const ctx = canvas.getContext('2d')
  if (!ctx) return

  const mobile = /android|webos|iphone|ipad|ipod|blackberry|iemobile|opera mini/i.test(
    navigator.userAgent.toLowerCase(),
  )
  const koef = mobile ? 0.5 : 1
  let width = (canvas.width = koef * window.innerWidth)
  let height = (canvas.height = koef * window.innerHeight)

  ctx.fillStyle = 'rgba(0,0,0,1)'
  ctx.fillRect(0, 0, width, height)

  const heartPosition = (rad: number) => [
    Math.pow(Math.sin(rad), 3),
    -(15 * Math.cos(rad) - 5 * Math.cos(2 * rad) - 2 * Math.cos(3 * rad) - Math.cos(4 * rad)),
  ]

  const scaleAndTranslate = (pos: number[], sx: number, sy: number, dx: number, dy: number) => [
    dx + pos[0] * sx,
    dy + pos[1] * sy,
  ]

  window.addEventListener('resize', () => {
    width = canvas.width = koef * window.innerWidth
    height = canvas.height = koef * window.innerHeight
    ctx.fillStyle = 'rgba(0,0,0,1)'
    ctx.fillRect(0, 0, width, height)
  })

  const traceCount = mobile ? 20 : 50
  const pointsOrigin: number[][] = []
  const dr = mobile ? 0.3 : 0.1

  for (let i = 0; i < Math.PI * 2; i += dr) {
    pointsOrigin.push(scaleAndTranslate(heartPosition(i), 210, 13, 0, 0))
    pointsOrigin.push(scaleAndTranslate(heartPosition(i), 150, 9, 0, 0))
    pointsOrigin.push(scaleAndTranslate(heartPosition(i), 90, 5, 0, 0))
  }

  const heartPointsCount = pointsOrigin.length
  const targetPoints: number[][] = []
  const pulse = (kx: number, ky: number) => {
    for (let i = 0; i < pointsOrigin.length; i++) {
      targetPoints[i] = [kx * pointsOrigin[i][0] + width / 2, ky * pointsOrigin[i][1] + height / 2]
    }
  }

  const particles = Array.from({ length: heartPointsCount }, (_, i) => {
    const x = Math.random() * width
    const y = Math.random() * height
    return {
      vx: 0,
      vy: 0,
      speed: Math.random() + 5,
      q: Math.floor(Math.random() * heartPointsCount),
      D: 2 * (i % 2) - 1,
      force: 0.2 * Math.random() + 0.7,
      f: `hsla(0,${Math.floor(40 * Math.random() + 60)}%,${Math.floor(60 * Math.random() + 20)}%,.3)`,
      trace: Array.from({ length: traceCount }, () => ({ x, y })),
    }
  })

  const config = { traceK: 0.4, timeDelta: 0.01 }
  let time = 0

  const loop = () => {
    const n = -Math.cos(time)
    pulse((1 + n) * 0.5, (1 + n) * 0.5)
    time += (Math.sin(time) < 0 ? 9 : n > 0.8 ? 0.2 : 1) * config.timeDelta

    ctx.fillStyle = 'rgba(0,0,0,.1)'
    ctx.fillRect(0, 0, width, height)

    particles.forEach((u) => {
      const q = targetPoints[u.q]
      const dx = u.trace[0].x - q[0]
      const dy = u.trace[0].y - q[1]
      const length = Math.sqrt(dx * dx + dy * dy)

      if (length < 10) {
        if (Math.random() > 0.95) u.q = Math.floor(Math.random() * heartPointsCount)
        else {
          if (Math.random() > 0.99) u.D *= -1
          u.q = (u.q + u.D + heartPointsCount) % heartPointsCount
        }
      }

      u.vx += (-dx / length) * u.speed
      u.vy += (-dy / length) * u.speed
      u.trace[0].x += u.vx
      u.trace[0].y += u.vy
      u.vx *= u.force
      u.vy *= u.force

      for (let k = 0; k < u.trace.length - 1; k++) {
        u.trace[k + 1].x -= config.traceK * (u.trace[k + 1].x - u.trace[k].x)
        u.trace[k + 1].y -= config.traceK * (u.trace[k + 1].y - u.trace[k].y)
      }

      ctx.fillStyle = u.f
      u.trace.forEach((point) => ctx.fillRect(point.x, point.y, 1, 1))
    })

    requestAnimationFrame(loop)
  }
  loop()
})
</script>

<style scoped>
canvas {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.2);
}
</style>
