###  *houdini, clarisse, maya Demo*

Maya demo
-----------

 ::

    from rayvision_api.core import RayvisionAPI
    from rayvision_maya.analyze_maya import AnalyzeMaya
    from rayvision_sync.upload import RayvisionUpload
    from rayvision_sync.download import RayvisionDownload
    from rayvision_api.task.check import RayvisionCheck
    from rayvision_api.utils import update_task_info, append_to_task, append_to_upload

    # API Parameter
    render_para = {
        "domain": "jop.foxrenderfarm.com", # If it doesn't work, you can use "task.foxrenderfarm.com"
        "platform": "62",
        "access_id": "xxxx",
        "access_key": "xxxx",
    }

    api = RayvisionAPI(access_id=render_para['access_id'],
                       access_key=render_para['access_key'],
                       domain=render_para['domain'],
                       platform=render_para['platform'])

    # Step1:Analyze CG File
    analyze_info = {
        "cg_file": r"D:\files\CG FILE\muti_layer_test.ma",
        "workspace": "c:/workspace",
        "software_version": "2019",
        "project_name": "Project1",
        "plugin_config": {
            "mtoa": "3.2.1.1"
        },
        "platform": render_para['platform']
    }
    analyze_obj = AnalyzeMaya(**analyze_info)
    analyze_obj.analyse()


    # Step2: Add some custom parameters, or update the original parameter value
    update_task = {
        "pre_frames": "100",
        "stop_after_test": "1"
    }
    update_task_info(update_task, analyze_obj.task_json)

    custom_info_to_task = {}
    append_to_task(custom_info_to_task, analyze_obj.task_json)

    custom_info_to_upload = []
    append_to_upload(custom_info_to_upload, analyze_obj.upload_json)

    # Step3: Set platform hardware configuration information. (Multiple models can be entered; Default is to select all)
    hardware_config = {
        "model": ["28C"],  # Platform CPU: 28C or Platform GPU: 1080Ti or 2080Ti
        "ram": "128GB",  # memory: 64GB or 128GB
        "gpuNum": None  # GPU platform requires input like 2*GPU, if CPU platform it is None
    }

    # Step4:Check json files
    check_obj = RayvisionCheck(api, analyze_obj)
    task_id = check_obj.execute(hardware_config, analyze_obj.task_json, analyze_obj.upload_json)


    # Step5: Transmission
    """
    There are two ways to upload the transmission:
    Upload_method: 1: upload four json files and upload the resource file according to upload.json;
                   2: json files and resources are uploaded separately;
    """
    CONFIG_PATH = {
        "tips_json_path": analyze_obj.tips_json,
        "task_json_path": analyze_obj.task_json,
        "asset_json_path": analyze_obj.asset_json,
        "upload_json_path": analyze_obj.upload_json,
    }
    upload_obj = RayvisionUpload(api)
    """
    The default of the test demo is to upload json and resource files at the same time,
    and users can choose their own upload method according to the actual situation.
    """
    upload_method = 1
    if upload_method == 1:
        # step4.1:Json files are uploaded in conjunction with CG resources
        upload_obj.upload(str(task_id), **CONFIG_PATH)
    elif upload_method == 2:
        # step4.2:CG resource files and json are uploaded separately
        upload_obj.upload_asset(upload_json_path=CONFIG_PATH["upload_json_path"])
        upload_obj.upload_config(str(task_id), list(CONFIG_PATH.values()))


    # Step6:Submit Task
    api.submit(int(task_id))


    # Step7:Download
    download = RayvisionDownload(api)
    # All complete before the automatic start of uniform download.
    # download.auto_download_after_task_completed([task_id])
    # Poll download (automatic download for each completed frame)
    download.auto_download([int(task_id)])


Houdini demo
-------------
 ::

    from rayvision_api.core import RayvisionAPI
    from rayvision_houdini.analyze_houdini import AnalyzeHoudini
    from rayvision_sync.upload import RayvisionUpload
    from rayvision_sync.download import RayvisionDownload
    from rayvision_api.task.check import RayvisionCheck
    from rayvision_api.utils import update_task_info, append_to_task, append_to_upload

    # API Parameter
    render_para = {
        "domain": "jop.foxrenderfarm.com",  # If it doesn't work, you can use "task.foxrenderfarm.com"
        "platform": "62",
        "access_id": "xxxx",
        "access_key": "xxxx",
    }

    api = RayvisionAPI(access_id=render_para['access_id'],
                       access_key=render_para['access_key'],
                       domain=render_para['domain'],
                       platform=render_para['platform'])

    # Step1:Analyze CG File
    analyze_info = {
        "cg_file": r"D:\houdini\CG file\flip_test_slice4.hip",
        "workspace": "c:/workspace",
        "software_version": "17.5.293",
        "project_name": "Project1",
        "plugin_config": {
            'renderman': '22.6'
        },
        "platform": render_para['platform']
    }
    analyze_obj = AnalyzeHoudini(**analyze_info)
    analyze_obj.analyse()


    # Step2: Add some custom parameters, or update the original parameter value
    update_task = {
        "pre_frames": "100",
        "stop_after_test": "1"
    }
    update_task_info(update_task, analyze_obj.task_json)

    custom_info_to_task = {}
    append_to_task(custom_info_to_task, analyze_obj.task_json)

    custom_info_to_upload = []
    append_to_upload(custom_info_to_upload, analyze_obj.upload_json)

    # Step3: Set platform hardware configuration information. (Multiple models can be entered; Default is to select all)
    hardware_config = {
        "model": ["28C"],  # Platform CPU: 28C or Platform GPU: 1080Ti or 2080Ti
        "ram": "128GB",  # memory: 64GB or 128GB
        "gpuNum": None  # GPU platform requires input like 2*GPU, if CPU platform it is None
    }

    # Step4:Check json files
    check_obj = RayvisionCheck(api, analyze_obj)
    task_id = check_obj.execute(hardware_config, analyze_obj.task_json, analyze_obj.upload_json)


    # Step5: Transmission
    """
    There are two ways to upload the transmission:
    Upload_method: 1: upload four json files and upload the resource file according to upload.json;
                   2: json files and resources are uploaded separately;
    """
    CONFIG_PATH = {
        "tips_json_path": analyze_obj.tips_json,
        "task_json_path": analyze_obj.task_json,
        "asset_json_path": analyze_obj.asset_json,
        "upload_json_path": analyze_obj.upload_json,
    }
    upload_obj = RayvisionUpload(api)
    """
    The default of the test demo is to upload json and resource files at the same time,
    and users can choose their own upload method according to the actual situation.
    """
    upload_method = 1
    if upload_method == 1:
        # step3.1:Json files are uploaded in conjunction with CG resources
        upload_obj.upload(str(task_id), **CONFIG_PATH)
    elif upload_method == 2:
        # step3.2:CG resource files and json are uploaded separately
        upload_obj.upload_asset(upload_json_path=CONFIG_PATH["upload_json_path"])
        upload_obj.upload_config(str(task_id), list(CONFIG_PATH.values()))


    # Step6:Submit Task
    api.submit(int(task_id))


    # Step7:Download
    download = RayvisionDownload(api)
    # All complete before the automatic start of uniform download.
    # download.auto_download_after_task_completed([task_id])
    # Poll download (automatic download for each completed frame)
    download.auto_download([int(task_id)])


Clarisse demo
--------------

 ::

    from rayvision_api.core import RayvisionAPI
    from rayvision_clarisse.analyse_clarisse import AnalyzeClarisse
    from rayvision_sync.upload import RayvisionUpload
    from rayvision_sync.download import RayvisionDownload
    from rayvision_api.task.check import RayvisionCheck
    from rayvision_api.utils import update_task_info, append_to_task, append_to_upload

    # API Parameter
    render_para = {
        "domain": "jop.foxrenderfarm.com",  # If it doesn't work, you can use "task.foxrenderfarm.com"
        "platform": "62",
        "access_id": "xxxx",
        "access_key": "xxxx",
    }

    api = RayvisionAPI(access_id=render_para['access_id'],
                       access_key=render_para['access_key'],
                       domain=render_para['domain'],
                       platform=render_para['platform'])

    # Step1:Analyze CG File
    analyze_info = {
        "cg_file": r"D:\files\CG FILE\clarisse_test1.project",
        "workspace": "c:/workspace",
        "software_version": "clarisse_ifx_4.0_sp3",
        "project_name": "Project1",
        "plugin_config": {},
        "platform": render_para['platform']
    }
    analyze_obj = AnalyzeClarisse(**analyze_info)
    analyze_obj.analyse()


    # Step2:Add some custom parameters, or update the original parameter value
    update_task = {
        "pre_frames": "100",
        "stop_after_test": "1"
    }
    update_task_info(update_task, analyze_obj.task_json)

    custom_info_to_task = {}
    append_to_task(custom_info_to_task, analyze_obj.task_json)

    custom_info_to_upload = []
    append_to_upload(custom_info_to_upload, analyze_obj.upload_json)

    # Step3: Set platform hardware configuration information. (Multiple models can be entered; Default is to select all)
    hardware_config = {
        "model": ["28C"],  # Platform CPU: 28C or Platform GPU: 1080Ti or 2080Ti
        "ram": "128GB",  # memory: 64GB or 128GB
        "gpuNum": None  # GPU platform requires input like 2*GPU, if CPU platform it is None
    }

    # Step4:Check json files
    check_obj = RayvisionCheck(api, analyze_obj)
    task_id = check_obj.execute(hardware_config, analyze_obj.task_json, analyze_obj.upload_json)


    # Step5:Transmission
    """
    There are two ways to upload the transmission:
    Upload_method: 1:upload four json files and upload the resource file according to upload.json;
                   2:json files and resources are uploaded separately;
    """
    CONFIG_PATH = {
        "tips_json_path": analyze_obj.tips_json,
        "task_json_path": analyze_obj.task_json,
        "asset_json_path": analyze_obj.asset_json,
        "upload_json_path": analyze_obj.upload_json,
    }
    upload_obj = RayvisionUpload(api)
    """
    The default of the test demo is to upload json and resource files at the same time,
    and users can choose their own upload method according to the actual situation.
    """
    upload_method = 1
    if upload_method == 1:
        # Step5.1:Json files are uploaded in conjunction with CG resources
        upload_obj.upload(str(task_id), **CONFIG_PATH)
    elif upload_method == 2:
        # Step5.2:CG resource files and json are uploaded separately
        upload_obj.upload_asset(upload_json_path=CONFIG_PATH["upload_json_path"])
        upload_obj.upload_config(str(task_id), list(CONFIG_PATH.values()))


    # Step6:Submit Task
    api.submit(int(task_id))


    # Step7:Download
    download = RayvisionDownload(api)
    # All complete before the automatic start of uniform download.
    # download.auto_download_after_task_completed([task_id])
    # Poll download (automatic download for each completed frame)
    download.auto_download([int(task_id)])


3ds Max demo
--------------

 ::

    from rayvision_max.analyse_max import AnalyseMax
    from rayvision_api.core import RayvisionAPI
    from rayvision_api.task.check import RayvisionCheck
    from rayvision_api.utils import update_task_info, append_to_upload, append_to_task
    from rayvision_sync.download import RayvisionDownload
    from rayvision_sync.upload import RayvisionUpload

    # API Parameter
    render_para = {
        "domain": "jop.foxrenderfarm.com",  # If it doesn't work, you can use "task.foxrenderfarm.com"
        "platform": "62",
        "access_id": "xxxx",
        "access_key": "xxxx",
    }

    api = RayvisionAPI(access_id=render_para['access_id'],
                       access_key=render_para['access_key'],
                       domain=render_para['domain'],
                       platform=render_para['platform'])

    analyze_info = {
        "cg_file": r'D:\houdini\CG file\jh\jh.max',
        "software_version": "2018",
        "project_name": "Project1",
        "workspace": r"C:\workspace\max",
        "plugin_config": {},
        "renderable_camera": ["Camera001"],  #  Render all cameras by default without specifying the required cameras
        "platform": render_para['platform']
    }
    analyze_obj = AnalyseMax(**analyze_info)
    analyze_obj.analyse()

    # Step2: Add some custom parameters, or update the original parameter value
    update_task = {
        "pre_frames": "100",
        "stop_after_test": "1"
    }
    update_task_info(update_task, analyze_obj.task_json)

    custom_info_to_task = {}
    append_to_task(custom_info_to_task, analyze_obj.task_json)

    custom_info_to_upload = []
    append_to_upload(custom_info_to_upload, analyze_obj.upload_json)

    # Step3: Set platform hardware configuration information. (Multiple models can be entered; Default is to select all)
    hardware_config = {
        "model": ["28C"],  # Platform CPU: 28C or Platform GPU: 1080Ti or 2080Ti
        "ram": "128GB",  # memory: 64GB or 128GB
        "gpuNum": None  # GPU platform requires input like 2*GPU, if CPU platform it is None
    }

    # Step4:Check json files
    check_obj = RayvisionCheck(api, analyze_obj)
    task_id = check_obj.execute(hardware_config, analyze_obj.task_json, analyze_obj.upload_json)

    # Step5: Transmission
    """
    There are two ways to upload the transmission:
    Upload_method: 1: upload four json files and upload the resource file according to upload.json;
                   2: json files and resources are uploaded separately;
    """
    CONFIG_PATH = {
        "tips_json_path": analyze_obj.tips_json,
        "task_json_path": analyze_obj.task_json,
        "asset_json_path": analyze_obj.asset_json,
        "upload_json_path": analyze_obj.upload_json,
    }
    upload_obj = RayvisionUpload(api)
    """
    The default of the test demo is to upload json and resource files at the same time,
    and users can choose their own upload method according to the actual situation.
    """
    upload_method = 1
    if upload_method == 1:
        # Step5.1:Json files are uploaded in conjunction with CG resources
        upload_obj.upload(str(task_id), **CONFIG_PATH)
    elif upload_method == 2:
        # Step5.2:CG resource files and json are uploaded separately
        upload_obj.upload_asset(upload_json_path=CONFIG_PATH["upload_json_path"])
        upload_obj.upload_config(str(task_id), list(CONFIG_PATH.values()))

    # Step6:Submit Task
    api.submit(int(task_id))

    # Step7:Download
    download = RayvisionDownload(api)
    # All complete before the automatic start of uniform download.
    # download.auto_download_after_task_completed([task_id])
    # Poll download (automatic download for each completed frame)
    download.auto_download([int(task_id)])


C4D demo
------------

  ::

    from rayvision_api.core import RayvisionAPI
    from rayvision_api.task.check import RayvisionCheck
    from rayvision_api.utils import update_task_info, append_to_task, append_to_upload
    from rayvision_c4d.analyze_c4d import AnalyzeC4d
    from rayvision_sync.download import RayvisionDownload
    from rayvision_sync.upload import RayvisionUpload

    # API Parameter
    render_para = {
        "domain": "jop.foxrenderfarm.com",  # If it doesn't work, you can use "task.foxrenderfarm.com"
        "platform": "62",
        "access_id": "xxxxx",
        "access_key": "xxxxxx",
    }

    api = RayvisionAPI(access_id=render_para['access_id'],
                       access_key=render_para['access_key'],
                       domain=render_para['domain'],
                       platform=render_para['platform'])

    # Step1:Analyze CG File
    analyze_info = {
        "cg_file": r"D:\houdini\cg_file\ybt.c4d",
        "workspace": "c:/workspace",
        "software_version": "R22",
        "project_name": "Project1",
        "plugin_config": {}
    }
    analyze_obj = AnalyzeC4d(**analyze_info)
    analyze_obj.analyse(exe_path=r"C:\Program Files\Maxon Cinema 4D R22\Cinema 4D.exe")

    # Step2:Add some custom parameters, or update the original parameter value
    update_task = {
        "pre_frames": "100",
        "stop_after_test": "1"
    }
    update_task_info(update_task, analyze_obj.task_json)

    custom_info_to_task = {}
    append_to_task(custom_info_to_task, analyze_obj.task_json)

    custom_info_to_upload = []
    append_to_upload(custom_info_to_upload, analyze_obj.upload_json)

    # Step3: Set platform hardware configuration information. (Multiple models can be entered; Default is to select all)
    hardware_config = {
        "model": ["28C"],  # Platform CPU: 28C or Platform GPU: 1080Ti or 2080Ti
        "ram": "128GB",  # memory: 64GB or 128GB
        "gpuNum": None  # GPU platform requires input like 2*GPU, if CPU platform it is None
    }

    # Step4:Check json files
    check_obj = RayvisionCheck(api, analyze_obj)
    task_id = check_obj.execute(hardware_config, analyze_obj.task_json, analyze_obj.upload_json)

    # Step5:Transmission
    """
    There are two ways to upload the transmission:
    Upload_method: 1:upload four json files and upload the resource file according to upload.json;
                   2:json files and resources are uploaded separately;
    """
    CONFIG_PATH = {
        "tips_json_path": analyze_obj.tips_json,
        "task_json_path": analyze_obj.task_json,
        "asset_json_path": analyze_obj.asset_json,
        "upload_json_path": analyze_obj.upload_json,
    }
    upload_obj = RayvisionUpload(api, automatic_line=True)
    """
    The default of the test demo is to upload json and resource files at the same time,
    and users can choose their own upload method according to the actual situation.
    """
    upload_method = 1
    if upload_method == 1:
        # Step5.1:Json files are uploaded in conjunction with CG resources
        upload_obj.upload(str(task_id), **CONFIG_PATH)
    elif upload_method == 2:
        # Step5.2:CG resource files and json are uploaded separately
        upload_obj.upload_asset(upload_json_path=CONFIG_PATH["upload_json_path"])
        upload_obj.upload_config(str(task_id), list(CONFIG_PATH.values()))

    # Step6:Submit Task
    api.submit(int(task_id))

    # Step7:Download
    download = RayvisionDownload(api)
    # All complete before the automatic start of uniform download.
    # download.auto_download_after_task_completed([task_id])
    # Poll download (automatic download for each completed frame)
    download.auto_download([int(task_id)])


Blender demo
----------------

 ::

    from rayvision_api.core import RayvisionAPI
    from rayvision_api.task.check import RayvisionCheck
    from rayvision_api.utils import update_task_info, append_to_task, append_to_upload
    from rayvision_blender.analyze_blender import AnalyzeBlender
    from rayvision_sync.download import RayvisionDownload
    from rayvision_sync.upload import RayvisionUpload

    # API Parameter
    render_para = {
        "domain": "jop.foxrenderfarm.com",  # If it doesn't work, you can use "task.foxrenderfarm.com"
        "platform": "62",
        "access_id": "xxxx",
        "access_key": "xxxx",
    }

    api = RayvisionAPI(access_id=render_para['access_id'],
                       access_key=render_para['access_key'],
                       domain=render_para['domain'],
                       platform=render_para['platform'])

    # Step1:Analyze CG File
    analyze_info = {
        "cg_file": r"D:\houdini\cg_file\PRAM RENDER 1.blend",
        "workspace": "c:/workspace",
        "software_version": "2.81",
        "project_name": "Project1",
        "plugin_config": {},
        "platform": render_para['platform']
    }
    analyze_obj = AnalyzeBlender(**analyze_info)
    analyze_obj.analyse(exe_path=r"C:\Program Files (x86)\Blender Foundation\Blender\blender.exe")

    # Step2:Add some custom parameters, or update the original parameter value
    update_task = {
        "pre_frames": "100",
        "stop_after_test": "1"
    }
    update_task_info(update_task, analyze_obj.task_json)

    custom_info_to_task = {}
    append_to_task(custom_info_to_task, analyze_obj.task_json)

    # User-defined UPLOAD.JSON file path
    upload_json_path = r"D:\blender\upload.json"

    custom_info_to_upload = [
        r"D:\houdini\cg_file\PRAM RENDER 1.blend"
    ]

    append_to_upload(custom_info_to_upload, upload_json_path)

    # Step3: Set platform hardware configuration information. (Multiple models can be entered; Default is to select all)
    hardware_config = {
        "model": ["28C"],  # Platform CPU: 28C or Platform GPU: 1080Ti or 2080Ti
        "ram": "128GB",  # memory: 64GB or 128GB
        "gpuNum": None  # GPU platform requires input like 2*GPU, if CPU platform it is None
    }

    # Step4:Check json files
    check_obj = RayvisionCheck(api, analyze_obj)
    task_id = check_obj.execute(hardware_config, analyze_obj.task_json, analyze_obj.upload_json)

    # Step5:Transmission
    """
    There are two ways to upload the transmission:
    Upload_method: 1:upload four json files and upload the resource file according to upload.json;
                   2:json files and resources are uploaded separately;
    """
    CONFIG_PATH = {
        "tips_json_path": analyze_obj.tips_json,
        "task_json_path": analyze_obj.task_json,
        "asset_json_path": analyze_obj.asset_json,
    }
    upload_obj = RayvisionUpload(api, automatic_line=True)
    """
    The default of the test demo is to upload json and resource files at the same time,
    and users can choose their own upload method according to the actual situation.
    """
    upload_obj.upload_asset(upload_json_path=upload_json_path)
    upload_obj.upload_config(str(task_id), list(CONFIG_PATH.values()))

    # Step6:Submit Task
    api.submit(int(task_id))

    # Step7:Download
    download = RayvisionDownload(api)
    # All complete before the automatic start of uniform download.
    # download.auto_download_after_task_completed([task_id])
    # Poll download (automatic download for each completed frame)
    download.auto_download([int(task_id)])


Arnorld Standalone demo
-------------------------

.. warning::
   Arnorld Standalone does not have an automatic asset analysis function and requires customers to analyze asset files themselves

---------------

 ::

    import os
    import sys
    import time
    from rayvision_api.core import RayvisionAPI
    from rayvision_sync.upload import RayvisionUpload
    from rayvision_sync.download import RayvisionDownload
    from rayvision_api.task.check import RayvisionCheck
    from rayvision_api.utils import update_task_info, append_to_task, append_to_upload
    from rayvision_utils import utils, constants


    def write_config_json(analyze_info):
        """The initialization task.json."""
        workspace_temp = os.path.join(analyze_info.get('workspace'), str(int(time.time())))
        task_json = os.path.join(workspace_temp, 'task.json')
        upload_json = os.path.join(workspace_temp, 'upload.json')
        if not os.path.exists(workspace_temp):
            os.makedirs(workspace_temp, exist_ok=True)
        constants.TASK_INFO["task_info"]["input_cg_file"] = analyze_info.get('cg_file').replace("\\", "/")
        constants.TASK_INFO["task_info"]["project_name"] = analyze_info.get('project_name')
        constants.TASK_INFO["task_info"]["cg_id"] = '2003'
        constants.TASK_INFO["task_info"]["os_name"] = "1" if "win" in sys.platform.lower() else "0"
        constants.TASK_INFO["task_info"]["platform"] = analyze_info.get('platform')
        constants.TASK_INFO["software_config"] = {
            "plugins": analyze_info.get('plugin_config'),
            "cg_version": analyze_info.get('software_version'),
            "cg_name": 'Arnold Standalone'
        }
        constants.TASK_INFO["scene_info"] = constants.TASK_INFO["scene_info_render"] = {
            "common": {
                "frames": analyze_info.get('render_frames')
                }
        }

        utils.json_save(task_json, constants.TASK_INFO)
        if not os.path.exists(upload_json):
            utils.json_save(upload_json, {"asset": []})
        return task_json, upload_json

    # ***** API Flow *****
    # API Parameter
    render_para = {
        "domain": "jop.foxrenderfarm.com",  # If it doesn't work, you can use "task.foxrenderfarm.com"
        "platform": "62",
        "access_id": "xxxxx",
        "access_key": "xxxxx",
    }

    api = RayvisionAPI(access_id=render_para['access_id'],
                       access_key=render_para['access_key'],
                       domain=render_para['domain'],
                       platform=render_para['platform'])

    # Scene Parameter
    analyze_info = {
        "cg_file": r"D:\ys\render_scene\arnold_standa\Arnold_Standalone_V7220.0001.ass",
        "workspace": r"D:\ys\workspace\arnold_standalone",
        "software_version": "7.2.2.0",
        "project_name": "Project1",
        "plugin_config": {},
        "render_frames": "1-3[1]",
        "platform": render_para['platform']
    }

    # Step1: Add some custom parameters, or update the original parameter value
    # Step1 can also be set without setting
    task_json, upload_json = write_config_json(analyze_info)
    update_task = {
        "pre_frames": "000",
        "stop_after_test": "1",
    }
    update_task_info(update_task, task_json)

    custom_info_to_task = {}
    append_to_task(custom_info_to_task, task_json)

    custom_info_to_upload = [
        r"D:\ys\render_scene\arnold_standa\Arnold_Standalone_V7220.0001.ass"
    ]
    append_to_upload(custom_info_to_upload, upload_json)

    # Step2: Set platform hardware configuration information. (Multiple models can be entered; Default is to select all)
    hardware_config = {
        "model": ["28C"],  # Platform CPU: 28C or Platform GPU: 1080Ti or 2080Ti
        "ram": "128GB",  # memory: 64GB or 128GB
        "gpuNum": None  # GPU platform requires input like 2*GPU, if CPU platform it is None
    }

    # Step3:Check json files
    check_obj = RayvisionCheck(api)
    api.task._generate_task_id()
    task_id = check_obj.execute(hardware_config, task_json)

    # Step4: Transmission task.json files and resources are uploaded separately
    upload_obj = RayvisionUpload(api)

    # Step4.1: Upload resource file(upload.json)
    upload_obj.upload_asset(upload_json_path=upload_json, engine_type="raysyncproxy")
    # Step4.2: Upload task.json
    upload_obj.upload_config(str(task_id), [task_json], engine_type="raysyncproxy")

    # Step5:Submit Task
    api.submit(int(task_id))

    # Step6:Download
    download = RayvisionDownload(api)
    # All complete before the automatic start of uniform download.
    download.auto_download_after_task_completed([task_id], engine_type="raysyncproxy")
    # Poll download (automatic download for each completed frame)
    download.auto_download([int(task_id)], local_path=r"D:\test", download_filename_format="false", engine_type="raysyncproxy")
