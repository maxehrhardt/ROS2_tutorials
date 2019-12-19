# Python

## Launch files
### Implementation
1. In your package create a `/launch` folder
2. In this folder create a `name.launch.py` file
3. `example.launch.py`:

```python
    """Launch the example node"""
    import launch
    import launch.actions
    import launch.substitutions
    import launch_ros.actions

    def generate_launch_description():
        return launch.LaunchDescription([
            launch_ros.actions.Node('
                package='example', node_executable='example_entrypoint, node_name='example_node' , output='screen',
                emulate_tty=True, # This enables prints when a node was started with launch 
                ),
        ])
```
4. Add an entrypoint to  `setup.py`:
```python
...
    entry_points={
        'console_scripts': [
            'example_entrypoint = example.example_node:main'
        ]
...
```
And add the file path of the lanch file:
```python
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        (os.path.join('share', package_name), glob('launch/*.launch.py'))
    ],
```

6. Build and source:
```
colcon build
source install/setup.zsh
```
7. Launch your node
```
ros2 launch example example.launch.py
```

### Features
Launch arguments can be declared with:
```python
from launch.actions import DeclareLaunchArgument
...
DeclareLaunchArgument(
    'argument_name',
    default_value='default',
    description='Description of the argument'
) 
```
The arguments of a launch file can be shown with:
```
ros2 launch --show-args package_name name.launch.py
```

## Config files
1. In your package create a `/config` folder
2. In this folder create the file `config.yaml`:
```yaml
example_node:
    ros__parameters:
        test_parameter: 3.1415
        ...
```
In the first line needs to be the name of the node the parameters are defined for. Optionally one can use `/**:` to put the parameters in every node.

3. Add the file path of the config file to the `setup.py`:
```python
...
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        (os.path.join('share', package_name), glob('config/*.yaml'))
        (os.path.join('share', package_name), glob('launch/*.launch.py'))
    ],
...
```

4. When the parameters shoudl be loaded with a launch file edit the `example.launch.py` like:
```python
    """Launch the example node"""
    import launch
    import launch.actions
    import launch.substitutions
    import launch_ros.actions

    import os
    from ament_index_python.packages import get_package_share_directory

    def generate_launch_description():
        # Get the file path where the parametr files can be found
        config = os.path.join(
            get_package_share_directory('example'),
            'config.yaml'
        )

        return launch.LaunchDescription([
            launch_ros.actions.Node('
                package='example', node_executable='example_entrypoint, node_name='example_node' , output='screen',
                emulate_tty=True, # This enables prints when a node was started with launch 
                parameters=[config]),
        ])
```
The installation path of the config file needs to be given.