# yolox-tiny 各层级信息

## train-log



```
YOLOX(
  (backbone): YOLOPAFPN(
    (backbone): CSPDarknet(
      (stem): Focus(
        (conv): BaseConv(
          (conv): Conv2d(12, 24, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(24, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
      )
      (dark2): Sequential(
        (0): BaseConv(
          (conv): Conv2d(24, 48, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): CSPLayer(
          (conv1): BaseConv(
            (conv): Conv2d(48, 24, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(24, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(48, 24, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(24, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv3): BaseConv(
            (conv): Conv2d(48, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (m): Sequential(
            (0): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(24, 24, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(24, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(24, 24, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(24, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
          )
        )
      )
      (dark3): Sequential(
        (0): BaseConv(
          (conv): Conv2d(48, 96, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): CSPLayer(
          (conv1): BaseConv(
            (conv): Conv2d(96, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(96, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv3): BaseConv(
            (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (m): Sequential(
            (0): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(48, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(48, 48, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
            (1): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(48, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(48, 48, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
            (2): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(48, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(48, 48, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
          )
        )
      )
      (dark4): Sequential(
        (0): BaseConv(
          (conv): Conv2d(96, 192, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): CSPLayer(
          (conv1): BaseConv(
            (conv): Conv2d(192, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(192, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv3): BaseConv(
            (conv): Conv2d(192, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (m): Sequential(
            (0): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
            (1): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
            (2): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
          )
        )
      )
      (dark5): Sequential(
        (0): BaseConv(
          (conv): Conv2d(192, 384, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(384, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): SPPBottleneck(
          (conv1): BaseConv(
            (conv): Conv2d(384, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (m): ModuleList(
            (0): MaxPool2d(kernel_size=5, stride=1, padding=2, dilation=1, ceil_mode=False)
            (1): MaxPool2d(kernel_size=9, stride=1, padding=4, dilation=1, ceil_mode=False)
            (2): MaxPool2d(kernel_size=13, stride=1, padding=6, dilation=1, ceil_mode=False)
          )
          (conv2): BaseConv(
            (conv): Conv2d(768, 384, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(384, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
        )
        (2): CSPLayer(
          (conv1): BaseConv(
            (conv): Conv2d(384, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(384, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv3): BaseConv(
            (conv): Conv2d(384, 384, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(384, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (m): Sequential(
            (0): Bottleneck(
              (conv1): BaseConv(
                (conv): Conv2d(192, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
                (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
              (conv2): BaseConv(
                (conv): Conv2d(192, 192, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
                (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
                (act): SiLU(inplace=True)
              )
            )
          )
        )
      )
    )
    (upsample): Upsample(scale_factor=2.0, mode=nearest)
    (lateral_conv0): BaseConv(
      (conv): Conv2d(384, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
      (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
      (act): SiLU(inplace=True)
    )
    (C3_p4): CSPLayer(
      (conv1): BaseConv(
        (conv): Conv2d(384, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv2): BaseConv(
        (conv): Conv2d(384, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv3): BaseConv(
        (conv): Conv2d(192, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (m): Sequential(
        (0): Bottleneck(
          (conv1): BaseConv(
            (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
            (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
        )
      )
    )
    (reduce_conv1): BaseConv(
      (conv): Conv2d(192, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
      (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
      (act): SiLU(inplace=True)
    )
    (C3_p3): CSPLayer(
      (conv1): BaseConv(
        (conv): Conv2d(192, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv2): BaseConv(
        (conv): Conv2d(192, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv3): BaseConv(
        (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (m): Sequential(
        (0): Bottleneck(
          (conv1): BaseConv(
            (conv): Conv2d(48, 48, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(48, 48, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
            (bn): BatchNorm2d(48, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
        )
      )
    )
    (bu_conv2): BaseConv(
      (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
      (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
      (act): SiLU(inplace=True)
    )
    (C3_n3): CSPLayer(
      (conv1): BaseConv(
        (conv): Conv2d(192, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv2): BaseConv(
        (conv): Conv2d(192, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv3): BaseConv(
        (conv): Conv2d(192, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (m): Sequential(
        (0): Bottleneck(
          (conv1): BaseConv(
            (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
            (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
        )
      )
    )
    (bu_conv1): BaseConv(
      (conv): Conv2d(192, 192, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
      (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
      (act): SiLU(inplace=True)
    )
    (C3_n4): CSPLayer(
      (conv1): BaseConv(
        (conv): Conv2d(384, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv2): BaseConv(
        (conv): Conv2d(384, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (conv3): BaseConv(
        (conv): Conv2d(384, 384, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(384, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (m): Sequential(
        (0): Bottleneck(
          (conv1): BaseConv(
            (conv): Conv2d(192, 192, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
          (conv2): BaseConv(
            (conv): Conv2d(192, 192, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
            (bn): BatchNorm2d(192, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
            (act): SiLU(inplace=True)
          )
        )
      )
    )
  )
  (head): YOLOXHead(
    (cls_convs): ModuleList(
      (0): Sequential(
        (0): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
      )
      (1): Sequential(
        (0): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
      )
      (2): Sequential(
        (0): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
      )
    )
    (reg_convs): ModuleList(
      (0): Sequential(
        (0): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
      )
      (1): Sequential(
        (0): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
      )
      (2): Sequential(
        (0): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
        (1): BaseConv(
          (conv): Conv2d(96, 96, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
          (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
          (act): SiLU(inplace=True)
        )
      )
    )
    (cls_preds): ModuleList(
      (0): Conv2d(96, 5, kernel_size=(1, 1), stride=(1, 1))
      (1): Conv2d(96, 5, kernel_size=(1, 1), stride=(1, 1))
      (2): Conv2d(96, 5, kernel_size=(1, 1), stride=(1, 1))
    )
    (reg_preds): ModuleList(
      (0): Conv2d(96, 4, kernel_size=(1, 1), stride=(1, 1))
      (1): Conv2d(96, 4, kernel_size=(1, 1), stride=(1, 1))
      (2): Conv2d(96, 4, kernel_size=(1, 1), stride=(1, 1))
    )
    (obj_preds): ModuleList(
      (0): Conv2d(96, 1, kernel_size=(1, 1), stride=(1, 1))
      (1): Conv2d(96, 1, kernel_size=(1, 1), stride=(1, 1))
      (2): Conv2d(96, 1, kernel_size=(1, 1), stride=(1, 1))
    )
    (stems): ModuleList(
      (0): BaseConv(
        (conv): Conv2d(96, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (1): BaseConv(
        (conv): Conv2d(192, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
      (2): BaseConv(
        (conv): Conv2d(384, 96, kernel_size=(1, 1), stride=(1, 1), bias=False)
        (bn): BatchNorm2d(96, eps=0.001, momentum=0.03, affine=True, track_running_stats=True)
        (act): SiLU(inplace=True)
      )
    )
    (l1_loss): L1Loss()
    (bcewithlog_loss): BCEWithLogitsLoss()
    (iou_loss): IOUloss()
  )


```



## AlexNet



```
AlexNet(
  61.101 M, 100.000% Params, 0.716 GMac, 100.000% MACs, 
  (features): Sequential(
    2.47 M, 4.042% Params, 0.657 GMac, 91.804% MACs, 
    (0): Conv2d(0.023 M, 0.038% Params, 0.07 GMac, 9.848% MACs, 3, 64, kernel_size=(11, 11), stride=(4, 4), padding=(2, 2))
    (1): ReLU(0.0 M, 0.000% Params, 0.0 GMac, 0.027% MACs, inplace=True)
    (2): MaxPool2d(0.0 M, 0.000% Params, 0.0 GMac, 0.027% MACs, kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
    (3): Conv2d(0.307 M, 0.503% Params, 0.224 GMac, 31.316% MACs, 64, 192, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
    (4): ReLU(0.0 M, 0.000% Params, 0.0 GMac, 0.020% MACs, inplace=True)
    (5): MaxPool2d(0.0 M, 0.000% Params, 0.0 GMac, 0.020% MACs, kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
    (6): Conv2d(0.664 M, 1.087% Params, 0.112 GMac, 15.681% MACs, 192, 384, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (7): ReLU(0.0 M, 0.000% Params, 0.0 GMac, 0.009% MACs, inplace=True)
    (8): Conv2d(0.885 M, 1.448% Params, 0.15 GMac, 20.902% MACs, 384, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (9): ReLU(0.0 M, 0.000% Params, 0.0 GMac, 0.006% MACs, inplace=True)
    (10): Conv2d(0.59 M, 0.966% Params, 0.1 GMac, 13.936% MACs, 256, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (11): ReLU(0.0 M, 0.000% Params, 0.0 GMac, 0.006% MACs, inplace=True)
    (12): MaxPool2d(0.0 M, 0.000% Params, 0.0 GMac, 0.006% MACs, kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
  )
  (avgpool): AdaptiveAvgPool2d(0.0 M, 0.000% Params, 0.0 GMac, 0.001% MACs, output_size=(6, 6))
  (classifier): Sequential(
    58.631 M, 95.958% Params, 0.059 GMac, 8.195% MACs, 
    (0): Dropout(0.0 M, 0.000% Params, 0.0 GMac, 0.000% MACs, p=0.5, inplace=False)
    (1): Linear(37.753 M, 61.788% Params, 0.038 GMac, 5.276% MACs, in_features=9216, out_features=4096, bias=True)
    (2): ReLU(0.0 M, 0.000% Params, 0.0 GMac, 0.001% MACs, inplace=True)
    (3): Dropout(0.0 M, 0.000% Params, 0.0 GMac, 0.000% MACs, p=0.5, inplace=False)
    (4): Linear(16.781 M, 27.465% Params, 0.017 GMac, 2.345% MACs, in_features=4096, out_features=4096, bias=True)
    (5): ReLU(0.0 M, 0.000% Params, 0.0 GMac, 0.001% MACs, inplace=True)
    (6): Linear(4.097 M, 6.705% Params, 0.004 GMac, 0.573% MACs, in_features=4096, out_features=1000, bias=True)
  )
)
flops:  0.72 GMac params:  61.1 M

```

