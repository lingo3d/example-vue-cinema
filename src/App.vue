<script setup lang="ts">
import {
  World,
  Model,
  ThirdPersonCamera,
  Keyboard,
  types,
  Find,
  useSpring,
  HTML,
} from "lingo3d-vue";
import { computed, ref } from "vue";

const pose = ref("idle");
const person = ref<types.Model>();
const looking = ref(false);

const camY = computed(() => {
  if (looking.value) return 150;
  else return 0;
});

const camYSpring = useSpring({ to: camY });

const handleKeyPress = (key: string) => {
  if (key === "w") {
    pose.value = "walking";
    person.value?.moveForward(-5);
  }
};

const handleKeyUp = (key: string) => {
  if (key === "w") {
    pose.value = "idle";
  }
};
</script>

<template>
  <World default-light="studio">
    <Model
      src="cinema.glb"
      :scale="10"
      physics="map"
      :roughness-factor="0.4"
      :metalness-factor="0.5"
    >
      <Find
        name="screen0"
        @mouse-over="looking = true"
        @mouse-out="looking = false"
        :outline="looking"
      >
        <HTML v-if="looking">
          <div style="color: white">now playing: Doctor Strange</div>
        </HTML>
      </Find>
    </Model>
    <ThirdPersonCamera
      active
      mouse-control
      :inner-z="100"
      :inner-y="camYSpring"
    >
      <Model
        src="person.fbx"
        ref="person"
        physics="character"
        :animations="{ idle: 'Idle.fbx', walking: 'Walking.fbx' }"
        :animation="pose"
        :metalness-factor="-1"
      />
    </ThirdPersonCamera>
    <Keyboard @key-press="handleKeyPress" @key-up="handleKeyUp" />
  </World>
</template>
