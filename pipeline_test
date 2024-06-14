from typing import List, Union, Generator, Iterator
from pydantic import BaseModel
import requests

class Pipeline:
    """Stable Diffusion ImageGen pipeline"""

    class Valves(BaseModel):
        """Options to change from the WebUI"""

        API_BASE_URL: str = "https://lab.volcanotester.com/images/api/v1/generations"
        API_KEY: str = ""
        IMAGE_SIZE: str = "1024x1024"
        NUM_IMAGES: int = 1

    def __init__(self):
        self.type = "manifold"
        self.name = "Stable Diffusion ImageGen"

        self.valves = self.Valves()
        self.client = requests.Session()  # Initialize requests session for potential reuse

        self.pipelines = []  # Initialize pipelines list as an empty list

    async def on_startup(self) -> None:
        """This function is called when the server is started."""
        print(f"on_startup:{__name__}")

    async def on_shutdown(self):
        """This function is called when the server is stopped."""
        print(f"on_shutdown:{__name__}")

    async def on_valves_updated(self):
        """This function is called when the valves are updated."""
        print(f"on_valves_updated:{__name__}")

    def generate_images(self, prompt: str) -> List[str]:
        """Generate images using the Stable Diffusion API

        Args:
            prompt (str): The prompt for image generation

        Returns:
            List[str]: List of URLs to the generated images
        """
        headers = {
            "Authorization": f"Bearer {self.valves.API_KEY}"
        }
        payload = {
            "prompt": prompt,
            "num_images": self.valves.NUM_IMAGES,
            "size": self.valves.IMAGE_SIZE
        }
        
        response = self.client.post(self.valves.API_BASE_URL, json=payload, headers=headers)
        response.raise_for_status()
        data = response.json()
        
        return [image["url"] for image in data]

    def pipe(
        self, user_message: str, model_id: str, messages: List[dict], body: dict
    ) -> Union[str, Generator, Iterator]:
        print(f"pipe:{__name__}")

        image_urls = self.generate_images(user_message)

        message = ""
        for url in image_urls:
            message += f"![image]({url})\n"

        yield message
