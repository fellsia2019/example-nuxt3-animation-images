<template>
  <div ref="rootTitleSectionAbout" class="title-section-about">
    <!-- переиспользуемый враппер для первого блока на многих страницах, это паралакс блок -->
    <TitleSectionWrapper
      :mainBlockClasses="mainBlockClasses"
      class="title-section-about__title-section-wrapper"
    >
      <template #out-container>
        <div class="title-section-about__preview-animate preview-animate">
          <!-- фильтр враппер для "шумной" текстуры фона изображений или текста -->
          <TextureFilter
            :isTexture="true"
            class="preview-animate__item"
            :class="[
              {
                'preview-animate__item--is-show': item.isVisible,
                'preview-animate__item--current': index === indexCurrentPreview,
              },
              `preview-animate__item--${index + 1}`,
            ]"
            :style="[
              getCoordinateStyle(index),
              `transform:translate(${item.translate.x}%,${item.translate.y}%)`,
            ]"
            v-for="(item, index) in titleSectionPreviewList"
            :key="`preview-animate__item-${item.id}`"
          >
            <img class="preview-animate__preview-image" :src="item.img" />
          </TextureFilter>
        </div>
      </template>
      <div class="title-section-about__body">
        <GoBack class="title-section-about__link-back">Вернуться назад</GoBack>
        <div class="title-section-about__main">
          <h1 class="title-section-about__title">
            <span class="title-section-about__first-part-title">
              Lorem, ipsum.
              <span class="text-green-400">Lorem</span> Lorem, ipsum.
            </span>
            <span class="title-section-about__second-part-title">
              <span>и</span> <span class="text-green-400"> LoremLorem.</span>
            </span>
          </h1>
          <div class="title-section-about__other-info">
            <img
              src="/img/title-section-about/arc-right--2xl.svg"
              class="title-section-about__arc-right title-section-about__arc-right--2xl"
            />
            <img
              src="/img/title-section-about/arc-right--xl.svg"
              class="title-section-about__arc-right title-section-about__arc-right--xl"
            />
            <p class="title-section-about__subtitle">
              <span class="title-section-about__row-1-subtitle">
                Lorem LoremLorem Lorem
              </span>
              <span class="title-section-about__row-2-subtitle">
                и LoremLorem
              </span>
              <span class="title-section-about__row-3-subtitle">
                LoremLorem Lorem</span
              >
            </p>
          </div>
          <CustomButton
            class="title-section-about__button"
            size="LG"
            theme="PRIMARY"
            >Стать клиентом</CustomButton
          >
        </div>
      </div>
    </TitleSectionWrapper>
  </div>
</template>

<script lang="ts" setup>
import { throttle } from "lodash";

// тайлвинд классы для паралакс враппера
const mainBlockClasses =
  "min-h-[622px] md:min-h-[887px] xl:min-h-[938px] 2xl:min-h-[1041px] pt-[30px] pb-[60px] md:pt-[50px] md:pb-[70px] xl:pt-[90px] xl:pb-[131px] 2xl:pt-[111px] 2xl:pb-[226px]";

interface ICoordinate {
  x: number;
  y: number;
}

// объект для картинок, которые двигаются за мышкой
const titleSectionPreviewList = ref<
  Array<{
    id: number;
    img: string;
    isVisible: boolean;
    coordinates: ICoordinate;
    timeoutHidde: NodeJS.Timeout;
    translate: ICoordinate;
  }>
>([
  {
    id: 1,
    img: "/img/title-section-about/1.png",
    isVisible: false,
    coordinates: {
      x: 0,
      y: 0,
    },
    translate: {
      x: -50,
      y: -50,
    },
    timeoutHidde: setTimeout(() => {}),
  },
  {
    id: 2,
    img: "/img/title-section-about/2.png",
    isVisible: false,
    coordinates: {
      x: 0,
      y: 0,
    },
    translate: {
      x: -50,
      y: -50,
    },
    timeoutHidde: setTimeout(() => {}),
  },
  {
    id: 3,
    img: "/img/title-section-about/3.png",
    isVisible: false,
    coordinates: {
      x: 0,
      y: 0,
    },
    translate: {
      x: -50,
      y: -50,
    },
    timeoutHidde: setTimeout(() => {}),
  },
  {
    id: 4,
    img: "/img/title-section-about/4.png",
    isVisible: false,
    coordinates: {
      x: 0,
      y: 0,
    },
    translate: {
      x: -50,
      y: -50,
    },
    timeoutHidde: setTimeout(() => {}),
  },
]);

const rootTitleSectionAbout = ref<HTMLElement | null>(null);

// координаты отрисовки текущей img
const coordinate = ref<ICoordinate>({
  x: 0,
  y: 0,
});

// высчет координат отрисовки картинки
const getCoordinateStyle = (index: number) => {
  return `top:${titleSectionPreviewList.value[index].coordinates.y}px; left: ${titleSectionPreviewList.value[index].coordinates.x}px`;
};

// шаг нового draw
// коэфициенты x и y высчитывались по макету
// картинки разных размеров
const getDelta = computed<number>(() => {
  const coefficientsDelta: { [key: number]: ICoordinate } = {
    0: {
      x: 2.8,
      y: 2.34,
    },
    1: {
      x: 2.72,
      y: 2.51,
    },
    2: {
      x: 2.31,
      y: 4.13,
    },
    3: {
      x: 1.8,
      y: 1.8,
    },
  };

  const x =
    currentImgSizeSettings.value.width /
    coefficientsDelta[indexCurrentPreview.value].x;
  const y =
    currentImgSizeSettings.value.height /
    coefficientsDelta[indexCurrentPreview.value].y;

  return Math.sqrt(x ** 2 + y ** 2);
});

const currentImgSizeSettings = computed<{ width: number; height: number }>(
  () => {
    if (!rootTitleSectionAbout.value) return { width: 0, height: 0 };

    const height =
      rootTitleSectionAbout.value
        .querySelector(
          `.preview-animate__item--${indexCurrentPreview.value + 1}`
        )
        ?.getBoundingClientRect().height || 0;
    const width =
      rootTitleSectionAbout.value
        .querySelector(
          `.preview-animate__item--${indexCurrentPreview.value + 1}`
        )
        ?.getBoundingClientRect().width || 0;

    return {
      width,
      height,
    };
  }
);

const onMouseMove = (event: MouseEvent) => {
  delayedHidingController.onClearTimeout();

  const mouseVector = Math.sqrt(
    Math.abs(event.clientX - coordinate.value.x) ** 2 +
      Math.abs(event.clientY - coordinate.value.y) ** 2
  );

  if (mouseVector >= getDelta.value) {
    coordinate.value = { x: event.clientX, y: event.clientY };
    draw();
  }

  delayedHidingController.onSetTimeout();
};

// индекс текущего img
const indexCurrentPreview = ref(0);

const draw = () => {
  // индекс текущей img
  const index: number = drawIndex.next().value;

  // индекс img, которая на два шага позади текущей
  const prevIndex =
    index - 2 < 0
      ? titleSectionPreviewList.value.length + (index - 2)
      : index - 2;

  indexCurrentPreview.value = index;
  titleSectionPreviewList.value[prevIndex].isVisible = false;
  titleSectionPreviewList.value[index].isVisible = true;
  titleSectionPreviewList.value[index].coordinates = coordinate.value;
};

// генерируем айдишник img для отрисовки
function* drawHelper() {
  let index = 0;
  while (true) {
    if (index >= titleSectionPreviewList.value.length) index = 0;
    yield index++;
  }
}

const drawIndex: Generator<number> = drawHelper();

// если мышь останавливается, то картинки должны скрыться через 2.5с
const delayedHidingController: {
  onSetTimeout(): void;
  onClearTimeout(): void;
} = {
  onSetTimeout: () => {
    titleSectionPreviewList.value.forEach((item) => {
      item.timeoutHidde = setTimeout(() => {
        item.isVisible = false;
      }, 2500);
    });
  },
  onClearTimeout: () => {
    titleSectionPreviewList.value.forEach((item) => {
      clearTimeout(item.timeoutHidde);
    });
  },
};

onMounted(() => {
  setTimeout(() => {
    if (!process.client || !rootTitleSectionAbout.value) return;

    rootTitleSectionAbout.value.addEventListener(
      "mousemove",
      throttle(onMouseMove, 50)
    );
  });
});
</script>

<style lang="scss">
.preview-animate {
  // .preview-animate__item
  &__item {
    @apply absolute xl:fixed z-[1] transition-all duration-300 xl:opacity-0;

    &--is-show {
      @apply opacity-100;
    }

    &--current {
      @apply xl:z-[2];
    }

    &--1 {
      @apply w-[63px] h-[80px] md:w-[112px] md:h-[155px];

      @media (max-width: 1226px) {
        @apply right-[109px] top-[190px] md:top-[301px] left-[unset] md:right-[175px] translate-x-[0] translate-y-[0] #{!important};
      }
    }

    &--2 {
      @apply w-[128px] h-[75px] md:w-[241px] md:h-[144px];
      @media (max-width: 1226px) {
        @apply top-[224px] right-[23px] md:top-[368px] left-[unset] md:right-[7px] translate-x-[0] translate-y-[0] #{!important};
      }
    }

    &--3 {
      @apply w-[159px] h-[113px] md:w-[305px] md:h-[219px];
      @media (max-width: 1226px) {
        @apply top-[253px] md:top-[425px] left-[unset] right-[0] translate-x-[33%] md:translate-x-[50%] translate-y-[0] #{!important};
      }
    }

    &--4 {
      @apply md:w-[364px] md:h-[228px];
      @media (max-width: 1226px) {
        @apply hidden #{!important};
      }
    }
  }

  // .title-section-about__preview-image
  &__preview-image {
    @apply w-full h-full object-cover object-center;
  }
}

.title-section-about {
  @apply relative;

  // .title-section-about__arc-right
  &__arc-right {
    @apply absolute right-[0] top-[0] h-full w-full;

    // .title-section-about__arc-right--xl
    &--xl {
      @apply hidden;

      @screen xl {
        @apply block;
      }
      @screen 2xl {
        @apply hidden;
      }
    }

    // .title-section-about__arc-right--2xl
    &--2xl {
      @apply hidden;
      @screen 2xl {
        @apply block;
      }
    }
  }

  // .title-section-about__link-back
  &__link-back {
    @apply mb-[24px];

    @screen md {
      @apply mb-[40px];
    }
    @screen xl {
      @apply mb-[60px];
    }
    @screen 2xl {
      @apply mb-[80px];
    }
  }

  // .title-section-about__main
  &__main {
    @apply xl:relative;
  }

  // .title-section-about__title
  &__title {
    @apply text-head-mob uppercase mb-[16px];
    @screen md {
      @apply mb-[24px] text-head-tablet;
    }
    @screen xl {
      @apply mb-[0] text-head;
    }
  }

  // .title-section-about__first-part-title
  &__first-part-title {
    @apply lg:grid xl:block;
  }

  // .title-section-about__second-part-title
  &__second-part-title {
    @apply block xl:text-right;
  }

  // .title-section-about__other-info
  &__other-info {
    @apply z-[-1];
    @screen xl {
      @apply fixed right-[0] top-[98px] aspect-[656/750] w-[41vw] max-h-[calc(100vw-100px)];
    }
    @screen 2xl {
      @apply top-[unset] bottom-[0] aspect-[748/983] h-[86.3vh] w-[unset];
    }
  }

  // .title-section-about__subtitle
  &__subtitle {
    @apply mb-[32px]  top-[116px];

    @screen md {
      @apply mb-[56px];
    }
    @screen xl {
      @apply absolute mb-[0] left-[15.54%] top-[38.26%];
    }

    @screen 2xl {
      @apply left-[10.3%] top-[60.6%];
    }
  }

  // .title-section-about__row-1-subtitle
  &__row-1-subtitle {
    @apply block;
  }
  // .title-section-about__row-2-subtitle
  &__row-2-subtitle {
    @apply block ml-[35px];
  }
  // .title-section-about__row-3-subtitle
  &__row-3-subtitle {
    @apply block ml-[73px];
  }

  // .title-section-about__button
  &__button {
    @screen xl {
      @apply absolute bottom-[20px];
    }
    @screen 2xl {
      @apply static mt-[171px];
    }
  }
}
</style>
