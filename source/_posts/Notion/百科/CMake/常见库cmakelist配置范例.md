---
title: 常见库cmakelist配置范例.md
toc: true
date: 2021-12-22 09:25:00
---
# 常见库cmakelist配置范例

```makefile
# 构建项目名称
set(TARGET_NAME test)
```

## CUDA

```makefile
find_package(CUDA REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${CUDA_INCLUDE_DIRS})
```

## ZED2

```makefile
find_package(ZED 3 REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${ZED_INCLUDE_DIRS})
target_link_directories(${TARGET_NAME} PUBLIC ${ZED_LIBRARY_DIR})
```

## Boost

```makefile
find_package(Boost 1.65 REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${Boost_INCLUDE_DIRS})
target_link_libraries(${TARGET_NAME} PUBLIC ${Boost_LIBRARIES})
```

## OpenCV

```makefile
find_package(OpenCV REQUIRED)
target_include_directories(${TARGET_NAME} PUBLIC ${OpenCV_INCLUDE_DIRS})
target_link_libraries(${TARGET_NAME} PUBLIC ${OpenCV_LIBRARIES})
```

## Redis++

```makefile
find_path(REDIS_INCLUDE_DIRS "sw")
find_library(REDIS_LIBRARIES "redis++")
target_include_directories(${TARGET_NAME} PUBLIC ${REDIS_INCLUDE_DIRS})
target_link_libraries(${TARGET_NAME} PUBLIC ${REDIS_LIBRARIES})
```