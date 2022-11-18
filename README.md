# README.md

# Introduction

This dataset is retrieved from [movienet](https://movienet.github.io/) with 228 movies. These movies are well hierarchically annotated with subtitle, cast, shot, scene, synopsis structure.

# Download
You can download 36.2G total file on
https://bigai-research.s3.us-west-1.amazonaws.com/movienet-cleaned/MovieNet-cleaned-v1.0-202211111805.zip

# structure

```bash
.
├── all
│   ├── cast
│   │   ├── tt0047396.json
│   │   └──...
│   ├── img
│   │   ├── tt0047396
│   │   │   ├── 0.jpg
│   │   │   ├── 1.jpg
│   │   │   └── ...
│   │   └── ...
│   ├── scene
│   │   ├── tt0047396.json
│   │   └──...
│   ├── shot
│   │   ├── tt0047396.json
│   │   └── ...
│   ├── subtitle
│   │   ├── tt0047396.json
│   │   └── ...
│   └── synopsis
│       ├── tt0047396.json
│       └── ...
│
├── movie_id_list
├── movie_info.json
├── test
└── train
```

## movie_id_list

228 sorted movie tt_ids (movie IMDB id) in each line.

tt_id regular expression pattern is `tt[\d]{7}`

Note not all movie have the annotation we needed, we carefully select following movies to compose our dataset.

```bash
tt0047396
tt0048545
tt0049730
tt0052357
tt0056923
...-
```

## movie_info.json

Some import informations of the movie data. 

   `duration` : length of the movie, in units of seconds.
   `fps` :  frames per scond. Note: most of the time fps is not precise 24.
   `framecnt` : frames count.
   `width_height_ratio` : the img ratio cut from movie.
   `subtitle_num` : total subtitle count retrievaled from tt_id.srt data in ***movienet.***
   `shot_num` :  total shot count of whole movie.
   `scene_num` : total scene count of whole movie covering all shot.
   `synopsis_num` : scene count of movie, not covering all shot and not aligned with scene[TODO]. 
   `resolution` : the img resolution cut from movie [h ,w].

```bash
{
  "tt0047396": {
    "duration": 6746.49,
    "fps": 23.98,
    "framecnt": 161754,
    "width_height_ratio": 1.6555555555555554,
    "subtitle_num": 1306,
    "shot_num": 793,
    "scene_num": 124,
    "synopsis_num": 16,
    "resolution": [
      1192,
      720
    ]
  },
  "tt0048545": {
    ...
  }
  ...
}
```

## all

All the datasets without train-test split will store here.

## train / test

 [TODO] need to split

## cast

This file have the information of cast and chatacter boundary bounding box.

**cast/tt0047396.json**

```bash
{
  "cast_pid_list": [
    "nm0000071",
    "nm0000038",
    "other",
    ...
  ],
  "cast_bbox": [
    {
      "cast_id": 0,
      "cast_pid": "nm0046564",
      "shot_id": 4,
      "img_id": 1,
      "bbox": [
        499,
        272,
        644,
        628
      ]
    },
    {
      "cast_id": 1,
    ...
    }
    ...
  ]
}
```

`cast_pid_list`  All cast_pids annotated in the movie(≤10). Regular expression pattern `tt[\d]{7}`, other cast are annotated with ‘other’.

`cast_bbox`  the boundary bounding box of cast in image

`cast_id` the cast id list in a movie, begin with 0

`cast_pid` cast_pid

`shot_id` shot_id

`img_id` [0,1,2] denote the ori (shot_id)_(img_id) in movienet 240P keyframe

`bbox`   [top_left_x, top_left_y, bottom_right_x, bottom_right_y]

## img

The dataset has 3 frames retrievaled for one shot, all the img formats are .jpg. The resolutions of imgs from the same movie are the same. You can find the resolution of each movie from ./movie_info.json. The regular expression pattern is `{shot_id}_{img_id}`.


## scene

In this file, we classifier the full movie shots into scene. Some of the shots at the end of movie do not have scene label, and we made the completion.

### scene/tt0047396.json

```bash
[
  {
    "scene_id": 0,
    "shot_id": [
      0,
      0
    ]
  },
  {
    "scene_id": 1,
    ...
  }
...
]
```

`scene_id` scene_id begin with 0; you can find the total sum of it in ./movie_info

`shot_id` shot_id in a scene [start_shot_id, end_shot_id], every scene has its shot

## shot

Shot is the basic structure level in our dataset, and all other hierarchal structures are aligned to it. We use the middle frame of the 3 in movienet shot. 

### shot/tt0047396.json

```bash
[
  {
    "shot_id": 0,
    "scene_id": 0,
    "subtitle_id": []
    "cast_pid": []
  },
  {
    "shot_id": 1,
    ...
  }
  ...
]
```

`shot_id` begins with 0

`scene_id` the scene id of shot, every scene has its

`subtitle_id` if the shot have subtitle, represent as [subtitle_start_id, subtitle_end_id], if not represent as []

`cast_pid` if the shot have cast annotated,  represent as [subtitle_start_id, subtitle_end_id], if not represent as []

## subtitle

The subtitles are retrievaled from original .srt data, note than the original .srt file do not have the same encoding mode, trying utf-8,utf-16,ISO-8859-1 is necessary.

### subtitle/tt0047396.json

```json
[
  {
    "subtitle_id": 0,
    "sentence": "Men, are you over 40?"
  },
  {
    "subtitle_id": 1,
    ...
  }
  ...
]
```

`subtitle_id` begins with 0, a id means a timestamp in original .srt file.

`sentence` the whole sentences in one timestamp, even those there are two sentences in one timestamp, we regard them as one sentence.

## synopsis

The synopsis is some descriptions to the movie. All synopsis can compose to a full summary, but not all shot will be covered.

### synopsis/tt0047396.json

```bash
[
  {
    "synopsis_id": 0,
    "shot_id": [
      3,
      30
    ],
    "scene_id": [
      1,
      9
    ]
    "sentence": "L.B. \"Jeff\" Jeffries (James Stewart) recuperates from a broken leg during a sweltering New York summer. As a successful photographer, he's known for taking difficult pictures no one else can get, including the one of an out-of-control race car which smashed his camera and broke his leg an instant after it was snapped. Jeffries lives in a small apartment, and spends his time in a wheelchair looking out the rear window into the courtyard of the building; he can also see into the lives of all his neighbors, catching glimpses of their daily routines. It's the sort of thing only an invalid might do, watching them eat, clean, sleep and argue. There's the girl who exercises in her underwear, the married couple who sleep on their small balcony to beat the heat, the struggling songwriter working at his piano; and there's the salesman who lives across the courtyard from Jeffries, the one with the nagging bedridden wife. They seem to fight all too often."
  },
  {
    "synopsis_id": 1,
    ...
  }
  ...
]
```

`synopsis_id` begins with 0.

`shot_id` [shot_start_id, shot_end_id] comes from the original movienet. Note it is not aligned with the scene edge.

`scene_id` [shot_start_id, shot_end_id] aligned by ourselves.

`sentence` the sentence in the synopsis, not it is not the same as subtitle sentence.
